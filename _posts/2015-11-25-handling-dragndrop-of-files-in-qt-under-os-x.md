---
id: 1248
title: 'Handling drag&#8217;n&#8217;drop of files in Qt under OS X'
date: 2015-11-25T18:00:02+00:00
author: latobcode
layout: post
guid: http://code.jamming.com.ua/?p=1248
permalink: /handling-dragndrop-of-files-in-qt-under-os-x/
categories:
  - C++
  - Qt
tags:
  - drag
  - drop
  - mac
  - model
  - os x
  - qml
  - qt
  - url
---
If you ever tried to handle drag&#8217;n&#8217;drop files in your Qt application, you would usually come up with the code like the following.
  
First of all you will need a Drop Area somewhere in your application, which will handle drops

<pre><code class="language-javascript">DropArea {
  anchors.fill: parent
  onDropped: {
    if (drop.hasUrls) {
      var filesCount = yourCppModel.dropFiles(drop.urls)
      console.log(filesCount + ' files added via drag&drop')
    }
 }
}</code></pre>

Where _yourCppModel_ is a model exposed to Qml in main.cpp or wherever like this:

<pre><code class="language-clike">QQmlContext *rootContext = engine.rootContext();
rootContext-&gt;setContextProperty("yourCppModel", &myCppModel);
</code></pre>

and <code class="language-clike">int dropFiles(const QList&lt;QUrl&gt; &urls)</code> is just an ordinary method exposed to QML via _`Q_INVOKABLE`_ attribute.

You will sure notice everything works fine unless you&#8217;re working under OS X. In OS X instead of QUrls to local files you will get something like this: _ `file:///.file/id=6571367.2773272/`_. There&#8217;s a bug in Qt for that and it even looks closed, but it still doesn&#8217;t work for me that&#8217;s why I&#8217;ve implemented my own helper using mixing of Objective-C and Qt-C++ code.

<!--more-->

I&#8217;ve added a `osxnshelper.h` and `osxnshelper.mm` source file with helper method to my project:

<pre><code class="language-clike">#include &lt;Foundation/Foundation.h&gt;
#include &lt;QUrl&gt;

QUrl fromNSUrl(const QUrl &url) {
    NSURL *nsUrl = url.toNSURL();
    NSString *path = nsUrl.path;

    QString qtString = QString::fromNSString(path);
    return QUrl::fromLocalFile(qtString);
}
</code></pre>

and added it into the .pro file with conditional define:

<pre><code class="language-clike">macx {
OBJECTIVE_SOURCES += \
    osxnsurlhelper.mm

LIBS += -framework Foundation
HEADERS += osxnsurlhelper.h
}
</code></pre>

Now I&#8217;m able to use this helper in my actual `dropFiles()` method:

<pre><code class="language-clike">int MySuperCppModel::dropFiles(const QList&lt;QString&gt; &urls)
{
    QList&lt;QString&gt; localUrls;

#ifdef Q_OS_MAC
    foreach (const QUrl &url, urls) {
        QUrl localUrl = fromNSUrl(url);
        localUrls.append(localUrl);
    }
#else
    localUrls = urls;
#endif
    // ......
}
</code></pre>

That&#8217;s it. Now it works perfectly.