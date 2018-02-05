---
id: 1035
title: YaCE contester engine
date: 2013-08-26T21:43:47+00:00
author: latobcode
layout: post
guid: http://latobcode.wordpress.com/?p=1035
permalink: /yace-contester-engine/
tagazine-media:
  - 'a:7:{s:7:"primary";s:59:"http://latobcode.files.wordpress.com/2013/08/yacescheme.jpg";s:6:"images";a:1:{s:59:"http://latobcode.files.wordpress.com/2013/08/yacescheme.jpg";a:6:{s:8:"file_url";s:59:"http://latobcode.files.wordpress.com/2013/08/yacescheme.jpg";s:5:"width";i:1329;s:6:"height";i:774;s:4:"type";s:5:"image";s:4:"area";i:1028646;s:9:"file_path";b:0;}}s:6:"videos";a:0:{}s:11:"image_count";i:1;s:6:"author";s:8:"20401582";s:7:"blog_id";s:8:"53632187";s:9:"mod_stamp";s:19:"2013-08-26 20:15:57";}'
categories:
  - Linux
  - Programming
tags:
  - acm
  - bash
  - contest
  - engine
  - programming
  - ruby
  - test
  - yace
---
This is not-a-success story about one of my projects, YaCE (Yet another Contester Engine). It's a system, written in Ruby and Bash which can check ACM problems solution validity. It can be scaled on a number of nodes, accessible via network (VMs or real machines).

The main reason to create it was to rewrite existing system which we used to train for ACM contests. "ACM Contester" was not scalable enough and it didn't have precise enough measurements of used memory and cpu time, which is crucial for testing our solutions.

I wanted to make it scalable and predictable. I wanted to write it in Ruby on Linux and for Linux.
  
YaCE has been started 2 years ago and last commit was made 11 months ago.

<!--more-->

YaCE uses primitive yet effective message queuing system over file system. It is also web-oriented.

Here it is some basic workflow:

[<img class="aligncenter size-medium wp-image-1036" alt="YaCEscheme" src="http://code.jamming.com.ua/wp-content/uploads/2013/08/yacescheme.jpg?w=300" width="622" height="363" srcset="http://code.jamming.com.ua/wp-content/uploads/2013/08/yacescheme.jpg 1329w, http://code.jamming.com.ua/wp-content/uploads/2013/08/yacescheme-300x175.jpg 300w, http://code.jamming.com.ua/wp-content/uploads/2013/08/yacescheme-768x447.jpg 768w, http://code.jamming.com.ua/wp-content/uploads/2013/08/yacescheme-1024x596.jpg 1024w" sizes="(max-width: 622px) 100vw, 622px" />](http://code.jamming.com.ua/wp-content/uploads/2013/08/yacescheme.jpg)

  * User somehow submits a solution on a website. It’s saved with some name somewhere.
  * Websites runs some quite simple init script, which starts checking process creating a basic pending task
  * Scheduler regularly checks if there are some pending tasks and if they are, it tries to schedule them to cluster node (when task is compiled, it depends forever to that node, where it’s compiled or saved). Scheduling means that appropriate task is copied to some abstract storage with queue structure of specified cluster node.
  * Worker manager manages some number of workers. Each worker depends on some cluster node. When a task is scheduled to some node, worker starts to execute this task at node using bash scripts on that node.
  * When worker task finishes, worker creates next task at all tasks queue. Each worker task can fail and appropriate result is added as _get_result task_ to all tasks queue
  * All _get_result tasks_ should be handled by some website mechanism, which will produce gui representation of task results.

Connection between scheduler and all tasks queue is implemented using tcp connection, that’s why it can be placed on some other machine. Also, worker connects to node using SSHv2, that’s why cluster nodes can be anything with ssh access (e.g. virtual machine, physical server etc)

This project has a great start but now is not required. I look sometimes at a <a title="YaCE GitHub page" href="https://github.com/Ribtoks/yace" target="_blank" class="broken_link">GitHub page </a>and fix some issues. I hope it would be useful someday.