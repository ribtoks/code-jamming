---
id: 243
title: 'Ruby style loops in C#'
date: 2012-10-31T13:44:18+00:00
author: latobcode
layout: post
guid: http://latobco.wordpress.com/2012/10/31/ruby-style-loops-in-c/
permalink: /ruby-style-loops-in-c/
tagazine-media:
  - 'a:7:{s:7:"primary";s:0:"";s:6:"images";a:0:{}s:6:"videos";a:0:{}s:11:"image_count";i:0;s:6:"author";s:8:"20401582";s:7:"blog_id";s:8:"41116138";s:9:"mod_stamp";s:19:"2012-10-31 13:51:18";}'
original_post_id:
  - "243"
categories:
  - Programming
tags:
  - .net
  - csharp
  - downto
  - loops
  - ruby
  - ruby-like
  - step
  - times
  - upto
---
[Ruby style loops in C#](http://blog.naiznoiz.com/2009/03/c-loops-ruby-style "Ruby style loops in C#")

Simple and useful solution to use ruby-like loops in C#. Also, you can add extensions

<!--more-->

[sourcecode language=&#8221;csharp&#8221;]
  
public struct MyInt
  
{
      
public delegate void Loop(int i);

private delegate int Aggregate(int aggregatedValue, int nextValue);

private delegate int LoopDirection(int prev);

private delegate bool LoopCondition(int a, int b);

private int i;
      
private int start;
      
private int end;
      
private LoopDirection direction;
      
private LoopCondition condition;

public static MyInt Int(int i)
      
{
          
return new MyInt(i);
      
}

public MyInt(int i)
      
{
          
this.i = i;
          
this.start = 0;
          
this.end = 0;
          
this.direction = null;
          
this.condition = null;
      
}

public MyInt Times()
      
{
          
return MyInt.Int(0).Step(this.i &#8211; 1, 1);
      
}

public MyInt DownTo(int i)
      
{
          
return this.Step(i, 1);
      
}

public MyInt UpTo(int i)
      
{
          
return this.Step(i, 1);
      
}

public MyInt Step(int end, int step)
      
{
          
this.start = this.i;
          
this.end = end;
          
if (start <= end)
          
{
              
direction = x => x + step;
              
condition = (a, b) => a <= b;
          
}
          
else
          
{
              
direction = x => x &#8211; step;
              
condition = (a, b) => a >= b;
          
}
          
return this;
      
}

public void Do(Loop del)
      
{
          
for (int iter = start; condition(iter, end); iter = direction(iter))
          
{
              
del(iter);
          
}
      
}
  
}

public static class MyIntExtension
  
{
      
public static MyInt Step(this int value, int end, int step)
      
{
          
return MyInt.Int(value).Step(end, step);
      
}

public static MyInt DownTo(this int value, int i)
      
{
          
return MyInt.Int(value).DownTo(i);
      
}

public static MyInt UpTo(this int value, int i)
      
{
          
return MyInt.Int(value).UpTo(i);
      
}

public static MyInt Times(this int value)
      
{
          
return MyInt.Int(value).Times();
      
}
  
}
  
[/sourcecode]