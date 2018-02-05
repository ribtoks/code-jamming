---
id: 984
title: Emacs hangs on large file with DOS-encoded end of lines
date: 2013-06-08T22:28:16+00:00
author: latobcode
layout: post
guid: http://latobco.wordpress.com/?p=984
permalink: /emacs-hangs-on-large-file-with-dos-encoded-end-of-lines/
original_post_id:
  - "984"
categories:
  - Emacs
  - Programming
tags:
  - dos
  - Emacs
  - eol
  - hang
  - inhibit-eol-convension
  - unix
---
I&#8217;ve had a quite big C++ source file, copied from Windows with _^M_ symbol after each line. After any scrolling downside Emacs 24.2 hanged up and the only option was to kill it.

Due to the fact that my _.emacs_ configuration is quite big, I started with binary search, commenting out parts of lisp code. After few minutes I&#8217;ve figured out, that the problem lies in _.gnu-emacs_ file, so I moved with binary search to that file.

Within 5 minutes, source of the problem was found:

    (setq-default inhibit-eol-conversion t)

After commenting this line, Emacs was resurrected!