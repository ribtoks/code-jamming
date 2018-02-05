---
id: 1142
title: Simple Gimp plugin for batch processing in Python
date: 2014-11-12T11:32:35+00:00
author: latobcode
layout: post
guid: http://code.jamming.com.ua/?p=1142
permalink: /simple-gimp-plugin-for-batch-processing-in-python/
categories:
  - Linux
  - Programming
tags:
  - api
  - gimp
  - plug-in
  - plugin
  - python
---
Not so far ago I needed to batch-process some images. The task was to resize them so they fit to given smallest resolution. Although Gimp has some batch-processing plugins, I wasn&#8217;t able to solve my problem with them. That&#8217;s why I&#8217;ve invented small little bike and I&#8217;d like to share with you workarounds and explanations of some Gimp plug-in development issues.

Your plugin can take as little as just one python file with call to function _register_ (_from gimpfu_) and passing to it some metadata and actual method of plugin. You can read more about parameters to register on <a title="Official python Gimp API" href="http://www.gimp.org/docs/python/index.html" target="_blank">official docs website</a>. But a few moments still need clarifications.

<!--more-->

As official documentation says,

<p style="padding-left: 30px;">
  <em>If the plugin is to be run on an image, the first parameter to the plugin function should be the image, and the second should be the current drawable</em>
</p>

 And if you&#8217;ll not change one of the parameters of _register_ function, which is called _image_types_, your plugin will be disabled without an image opened in GIMP. If you don&#8217;t know what to write there, just leave it blank (other possible values are &#8220;RGB\*&#8221;, &#8220;GRAY\*&#8221;).

Other parameters of _register_ function set actual parameters of you plugin main method with default values.

Sample call to register (you can find tons of them on the internet):

<pre><code class="language-clike">register(
    "python_fu_resize_max",
    "Scales the image to fit minimal size in megapixels",
    "Help here",
    "Your Name",
    "Your Name",
    "2014",
    "&lt;Toolbox&gt;/Tools/Resize images to min size...",
    "",
    [
        (PF_INT, "min_size", "Minimum image size", 6000000),
        (PF_BOOL, "copy", "Make a JPEG copy", TRUE),
        (PF_BOOL, "process_dir", "Process a directory", TRUE),
        (PF_DIRNAME, "path", "Directory to Open", "./"),
    ],
    [],
    plugin_main)</code>
</pre>

And declaration of _plugin_main_:

<pre>def plugin_main(minsize=6000000, savecopy=TRUE, processdir=TRUE, dirname="./"):</pre>

Don&#8217;t forget to make the script executable itself.

You can find <a href="https://raw.githubusercontent.com/Ribtoks/heap/master/gimp-scale-min-plugin/scale_min.py" target="_blank">complete example here</a>.