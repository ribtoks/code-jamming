---
title: 'Firefox hangs and eats 25% cpu! (with fix)'
date: 2013-06-12T22:50:19+00:00
author: "Taras Kushnir"
layout: post
permalink: /firefox-hangs-and-eats-25-cpu-with-fix/
categories:
  - Linux
tags:
  - addons
  - cpu
  - ff
  - firefox
  - hangs
  - sync
  - xmarks
---
This thing happened suddenly and I wasn't able to explain it. A moment ago Firefox worked without problems, but now it hanged and Task Manager showed 25% CPU usage. Firefox was "Not responding" and nothing helped. In safe mode it worked well, so addons were the problems. But which one? I have a dozen..

Such behavior was still even at home at OpenSUSE with FF (and with same strange 25%!!), so I started Firefox with addons disabled. Everything went smoothly.  So there was a problems in addons. After playing with them, I've found that Xmarks conflicts with Firefox Sync: both are produthing thousands of bookmarks in infinite loop.

(I've created a <a href="https://bugzilla.mozilla.org/show_bug.cgi?id=882423" target="_blank"><em>bugreport</em></a> in the Mozilla Bugzilla)

So disabling Xmarks solved the issue!

UPD: Bugzilla guys say "it's not their fault" and redirect to Xmarks bugzilla, so I reported an issue in FF addons window for Xmarks