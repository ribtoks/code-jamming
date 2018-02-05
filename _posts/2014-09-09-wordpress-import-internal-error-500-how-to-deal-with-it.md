---
title: 'WordPress import Internal Error 500: how to deal with it'
date: 2014-09-09T12:38:21+00:00
author: "Taras Kushnir"
layout: post
permalink: /wordpress-import-internal-error-500-how-to-deal-with-it/
categories:
  - Programming
tags:
  - import
  - internal error
  - refresh
  - wordpress
---
When you import really large website to WP, server process can crash because of different restrictions for PHP memory, request processing time, database connection time etc. To deal with it, you have several options:

  * one can find WP support forum with <a href="http://wordpress.org/support/topic/importing-wordpress-xml-fails-with-a-500-error" target="_blank">appropriate post</a> and solution: the simplier is to refresh page and click "Resend" which will repeat POST request and WP will continue to import data (it's smart enough to skip already imported posts)
  * another (wiser) solution is to split your import file to several smaller files and import them one by one. Import file is just an ordinary XML file, so you'll just have to copy-paste header and then split import data is fairly small parts.