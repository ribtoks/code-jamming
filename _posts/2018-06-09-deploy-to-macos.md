---
title: Deploying native apps to macOS
date: 2018-06-09T00:23:10+00:00
author: "Taras Kushnir"
layout: post
image: deploy-mac.jpg
categories:
  - Programming
tags:
  - macos
  - qt
  - native
  - package
---

## Level 0: warming up

Deploying anything anywhere has never been easy business. Deploying applications to desktop computers is no different. OS X (now macOS) had it's own solution of ["dll hell"](https://en.wikipedia.org/wiki/DLL_Hell): each application should be absolutely self contained. All the dependencies and dependencies of dependencies should be distributed within the "bundle" so apps won't conflict with other apps because of missing or outdated libraries.

Sounds good in theory, but what does it mean in practice? You have to collect all the dependencies somehow and fit them into your "bundle". Bundle structure usually looks like this:

    HelloWorld.app/
    - Contents/
      - Frameworks/
      - MacOS/
        - HelloWorld (executable)
      - Resources/
      - PlugIns/

Bundle is just a directory with suffix ".app" which magically converts it to the "bundle" where real executable is inside `Contents/MacOS/` directory, it's library dependencies live in `Frameworks/` directory and other resources in other appropriate places (`Resources/` or `PlugIns`).

To understand which dependencies does your `HelloWorld` executable have, you can use `otool` command line utility (part of XCode):

    > otool -L HelloWorld.app/Contents/MacOS/HelloWorld

        @rpath/QtNetwork.framework/Versions/5/QtNetwork 
	@rpath/QtCore.framework/Versions/5/QtCore 
	/System/Library/Frameworks/IOKit.framework/Versions/A/IOKit 
	@rpath/QtGui.framework/Versions/5/QtGui 
	/usr/lib/libc++.1.dylib 
	/usr/lib/libSystem.B.dylib 
        .....

It lists all dependencies of your app. You may ask what is `@rpath`? It's a special variable (a friend of `@executable_path` and `@load_path`) set by linker, but which value can be computed when your app is started or overriden with environmental `LD_LIBRARY_PATH` variable. For native applications built for debug it could be `/usr/lib`. For Qt applications it could be `/path/to/Qt5.6.2/5.6/clang_64/lib` or anything alike.

You can learn this path also using `otool` but this time with parameter `-l` and checking for `LC_PATH` block:

    > otool -l HelloWorld.app/Contents/MacOS/HelloWorld

        ......
        Load command 24
          cmd LC_RPATH
          cmdsize 56
          path /Users/user/Qt5.6.2/5.6/clang_64/lib (offset 12)
        ......

Also `otool -l` is pretty useful command to learn how does your application starts anyway. How does it know it's depedencies and so on (yes, they are all just listed in your app).

So obviously your user's computer probably will not have same path as the one on your computer so you have to change it. Remember bundle structure couple of passages above? All the libraries should live in `Frameworks/` directory so your application `@rpath` will point to `Frameworks/` and will be able to start using the libraries that come along.

In order to change `@rpath` (and other sections) you need `install_name_tool` utility (also comes with XCode) so in the end your app's `LC_RPATH` will point to `@executable_path/../Frameworks/` (where `@executable_path` is another magic variable with self-descriptive name).

### Qt world

If you're bundling a Qt app you can make use of `macdeployqt` utility which comes with Qt distribution and it can pretty much pack all the frameworks and libraries required for your app and missing in `/usr/lib/`.

    macdeployqt HelloWorld.app -no-strip -executable="HelloWorld.app/Contents/MacOS/HelloWorld" -qmldir=../src/

This command will bundle all the dependencies of the executable passed to it via command line. And this will work if your application only depends on some system and Qt libraries.

## Level 1: additional files

If your application depends on other resources (icon, videos, dictionaries etc.) that are not compiled into the executable, you also have to ship them using `Resources/` directory. I recommend creating a deployment script that will do that for you of course because doing it manually every time is very error-prone.

Deployment: as easy as:

    cp -v /path/to/your/file HelloWorld.app/Contents/Resources/

## Level 2: custom libraries

This is harder. Simple copying library or framework to `Frameworks/` in order to deploy it is not enough. Libraries as well as executables are of the same ELF (Executable and Linkable Format) format so they also have all these sections like `LC_LOAD_DYLIB` that need to be fixed. Also your executable will look for the library in a specific place that was set by the linker and this path also has to be fixed.

So in order to deploy custom libraries you have to first copy them to `Frameworks/`, fix your app and fix the library in case it has other transitive dependencies on your other libs.

    # copy library to Frameworks
    cp -v /path/to/library.so HelloWorld.app/Contents/Frameworks/

    # fix dependency of the main app
    install_name_tool -change "library.so" "@executable_path/../Frameworks/library.so" HelloWorld.app/Contents/MacOS/HelloWorld

    # and dependencies of dependencies
    install_name_tool -change "/usr/local/lib/$depend_lib" "@executable_path/../Frameworks/$depend_lib" "$lib"

## Level 3: additional applications

Say you need some additional application like a [crash handler](https://github.com/ribtoks/xpiks/blob/master/src/recoverty/README.md) to be deployed alongside with your main one. If you were skimming this post thoroughly then you know the answer: you copy it's dependencies to `Frameworks/` and fix `@rpath` using `install_name_tool`.

To be more precise first you need to choose where exactly your app will be with regards to the main app. Usually it's put alongside with it in the `Contents/MacOS` directory like this:

    HelloWorld.app/
      - Contents/
        - Frameworks/
        - Resources/
        - MacOS/
          - HelloWorld (main exe)
          - MyHelper.app/
            - Contents
              - MacOS/
                - MyHelper (additional exe)
              - Frameworks/
              - Resources/

Of course you can first create a fully self-contained bundle of your other app, but this will only increase total size of the bundle. What makes more sense is to reuse dependencies of the main app given that they cover smaller one. In order to do so you will need to change `@rpath` of the smaller executable to point to `Frameworks/` directory of the parent. Also sounds like a job for `install_name_tool` and a fresh couple of lines in your deployment script.

### Qt world

If your small additional application is also a Qt application (which is likely) then above is not enough. Problem is that Qt apps need also platform-specific plugins and resources shipped alongside with them. If your main app already has them deployed using `macdeployqt` then you can reuse them using `qt.conf` file. This is a magical file that `QApplication`-descendent classes look for in order to learn about the environment, parameters, paths to plugins and resources and what not.

So to reuse resources and platform-specific plugins of the main app you would need to create a `qt.conf` in `Resources/` directory of the dependent app with relative paths like this:

    > cat << EOF > "${ADDITIONAL_APP}/Contents/Resources/qt.conf"
    [Paths]
    Plugins = ../../../PlugIns
    Imports = ../../../Resources/qml
    Qml2Imports = ../../../Resources/qml
    EOF

## Bonus level: DMG creation

Of course it would be too easy if bundling an `.app` was everything that you needed to ship your application. In Apple world applications are usually shipped in `.dmg` files which are Apple Disk Image files. Good news is that if you only want to compress `.app` to `.dmg` you need pretty much one command:

    > hdiutil create -srcfolder "/path/to/HelloWorld.app" -volname "NameOfDMG" -fs HFS+ \
    -fsargs "-c c=64,a=16,e=16" -format UDRW -size ${SIZE}m "${DMG_TMP}"

Bad news is that it will lead to bad user experience. Imagine you open an installer and what you see is just ugly white background and some `HelloWorld.app` in top left corner left totally at your disposal. You should guess yourself what you want to do with it. Probably just close or trash immediately.

![Raw DMG]({{ "/assets/img/bundle-raw.png" | absolute_url }})

What people usually do is they set a pretty background image with some instructions and place a link to `Applications/` directory. Instructions usually visually explain to drag the `HelloWorld.app` to `Applications` in order to install it.

![Prettified DMG]({{ "/assets/img/bundle-background.png" | absolute_url }})

In order to do so you have to copy background file to `.background` directory of the DMG and reside it's window and icons inside to fit it. Mount your first dmg, do these manipulations and then reexport it from `Disk Utility` to read-only dmg. You can also automate these actions with a Apple Script but that's a topic for another post.

## The end

As you can see deploying desktop apps on macOS is a total hassle as soon as you have something more complex than `HelloWorld.app`. XCode/QtCreator can automate many things but custom libraries, additional applications and others require doing some push-ups with custom deployment scripts.

## References

There's an awesome article about [dynamic loading in Linux](https://amir.rachum.com/blog/2016/09/17/shared-libraries/). It explains about `rpath`, `runpath` and other quirks.