---
title: Problems when ejecting WD My Passport in Linux (OpenSUSE)
date: 2013-09-10T21:46:43+00:00
author: "Taras Kushnir"
layout: post
permalink: /problems-when-ejecting-wd-my-passport-in-linux-opensuse/
categories:
  - Linux
tags:
  - digital
  - eject
  - linux
  - opensuse
  - remove
  - safe
  - udisks
  - unmount
  - western
---
Struggled to safely eject my 1TB  Western Digital My Passport external hard drive in OpenSUSE. Googling regarding Western Digital was inefficient, because lots of people have been just saying not-such-a-good-things about WD products and nothing valuable regarding ejecting it.

Usual `eject` command had no success: hard drive continued to spin. I've even upgraded firmware to 1.49 on my Windows 7 machine at work. No succcess.

After some time googling I've found next working solution:

  * install `udisks` utility
  * run `sudo udisks --unmount /mount/point` from terminal
  * run `sudo udisks --detach /your/device` from terminal
  * profit

So, enjoy!