---
author: admin
comments: true
date: 2013-07-19 14:30:05+00:00
layout: post
slug: text-file-busy
title: Text file busy
wordpress_id: 730
categories:
- 开发运营
tags:
- linux
- shell
- text file busy
- 文件
---

最近在测试程序替换进程时，发现有时替换时提示“text file busy”

但是把文件rm了，就可以了。或者把进程给重启就ok了。

记录下。

后面找下为什么，网上说是因为文件在被使用，使用

fuser -cdkux filename

命令就可以了。

后续：

查看fuser如何使用；

为什么有时text file busy，有时不会，是不是文件太大了，内存放不下导致？



[http://blog.chinaunix.net/uid-83572-id-3331.html](http://blog.chinaunix.net/uid-83572-id-3331.html)

[http://www.cnblogs.com/276815076/archive/2011/10/10/2205682.html](http://www.cnblogs.com/276815076/archive/2011/10/10/2205682.html)
