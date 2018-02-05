---
title: Mustek 1248 UB scanner setup in linux (openSuSE 12.2)
date: 2013-02-10T15:05:18+00:00
author: latobcode
layout: post
permalink: /mustek-1248ub-setup-in-linux/
categories:
  - Programming
tags:
  - "1248"
  - 1248ub
  - linux
  - mustek
  - opensuse
  - scanner
---
I know a lot of instructions exist about how to setup Mustek 1248ub scanner in linux. All of them (from Ubuntu forum) refer to <a title="Sane drivers" href="http://www.meier-geinitz.de/sane/gt68xx-backend/" target="_blank">this</a> webpage, recommend you download appropriate driver, save it into _<span style="color:#666699;">/usr/share/sane/gt68xx</span>_ folder, add read permissions to all and be happy. But none of them told to uncomment <span style="color:#666699;"><em>gt68xx</em></span> line in <span style="color:#666699;"><em>/etc/sane.d/dll.conf</em></span> file! So, do it and <span style="color:#666699;"><em>sane-find-scanner</em></span> will do it best for you!

Also there are such situations, when <span style="color:#99ccff;"><em><span style="color:#666699;">sane-find-scanner</span></em></span> does not find scanner, but _<span style="color:#666699;">scanimage -L</span>_ and <span style="color:#666699;"><em>xsane</em></span> do well!