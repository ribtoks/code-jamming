---
id: 680
title: C++ dev via Emacs 24 in Windows 7 (visual studio includes)
date: 2013-03-13T08:23:35+00:00
author: latobcode
layout: post
guid: http://latobco.wordpress.com/?p=680
permalink: /c-dev-via-emacs-24-in-windows-7/
tagazine-media:
  - 'a:7:{s:7:"primary";s:0:"";s:6:"images";a:0:{}s:6:"videos";a:0:{}s:11:"image_count";i:0;s:6:"author";s:8:"20401582";s:7:"blog_id";s:8:"41116138";s:9:"mod_stamp";s:19:"2013-03-13 08:26:08";}'
original_post_id:
  - "680"
categories:
  - Emacs
  - Programming
tags:
  - autocomplete
  - cedet
  - Emacs
  - include
  - semantic
  - system
  - windows
---
Recently I had issue with CEDET 1.1: semantic was not able to parse header files from Visual Studio, when using

_(semantic-add-system-include &#8220;C:/Program Files/Microsoft Visual Studio 10.0/VS/Include&#8221; &#8216;c++-mode)_

But after looking through Visual Stuio headers in Studio itself, I&#8217;ve found some headers with defines, which are new to semantic, so you have to add them after this system include in your _.el_ files:

    (defun windows-semantic-hook ()
      (setq microsoft-base-dir 
            "C:/Program Files (x86)/Microsoft Visual Studio 14.0/VC/include")
      (add-to-list 'semantic-lex-c-preprocessor-symbol-file 
                   (concat microsoft-base-dir "/crtdefs.h"))
      (add-to-list 'semantic-lex-c-preprocessor-symbol-file 
                   (concat microsoft-base-dir "/yvals.h"))
      (add-to-list 'semantic-lex-c-preprocessor-symbol-file 
                   (concat microsoft-base-dir "/vadefs.h"))
      (add-to-list 'semantic-lex-c-preprocessor-symbol-file 
                   (concat microsoft-base-dir "/comdefsp.h"))
      (semantic-add-system-include microsoft-base-dir 'c++-mode)
      (add-to-list 'auto-mode-alist (cons microsoft-base-dir 'c++-mode)))
    
    (add-hook 'semantic-init-hooks 'windows-semantic-hook)