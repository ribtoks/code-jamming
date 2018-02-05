---
id: 1069
title: Solving mouse delays and lags in linux
date: 2013-11-16T22:09:52+00:00
author: latobcode
layout: post
guid: http://latobcode.wordpress.com/?p=1069
permalink: /solving-mouse-delays-and-lags-in-linux/
categories:
  - Linux
tags:
  - delay
  - kernel
  - lag
  - laptop-mode
  - linux
  - mouse
  - problem
  - wireless
---
I&#8217;ve been experiencing strange mouse delays and lags some time ago and now I&#8217;ve become tired of it. I&#8217;m using OpenSUSE with 3.7.10 kernel and first thing I tried was upgrading the kernel (to 3.12) which was quite useless. Googling related to my distro also wasn&#8217;t helpful.

The trick was in laptop-mode-tools which I&#8217;ve installed earlier to make my laptop battery life longer. There is an usb autosuspend options which just suspended my usb wireless mouse. So setting

    AUTOSUSPEND_USBTYPE_BLACKLIST="usbhid"

in the

    /etc/laptop-mode/conf.d/usb-autosuspend.conf

and running

    /etc/init.d/laptop-mode restart

was enough.