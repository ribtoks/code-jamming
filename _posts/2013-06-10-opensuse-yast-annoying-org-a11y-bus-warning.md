---
id: 989
title: OpenSUSE Yast annoying org.a11y.Bus warning
date: 2013-06-10T22:59:52+00:00
author: latobcode
layout: post
guid: http://latobcode.wordpress.com/?p=989
permalink: /opensuse-yast-annoying-org-a11y-bus-warning/
tagazine-media:
  - 'a:7:{s:7:"primary";s:0:"";s:6:"images";a:0:{}s:6:"videos";a:0:{}s:11:"image_count";i:0;s:6:"author";s:8:"20401582";s:7:"blog_id";s:8:"53632187";s:9:"mod_stamp";s:19:"2013-06-10 21:00:33";}'
categories:
  - Linux
  - Programming
tags:
  - at-spi2-core
  - linux
  - opensuse
  - org.a11y.Bus
  - warning
  - yast
---
Has anybody ever received this warning message when using YaST Gui?

&#8220;The name org.a11y.Bus was not provided by any .service files&#8221;

It appears with almost any GTK program and to solve it, you have to install _at-spi2-core_ package, which provides such name.