---
author: admin
comments: true
date: 2012-02-16 09:14:03+00:00
layout: post
slug: makefile-tips
title: makefile小技巧
wordpress_id: 303
categories:
- makefile
---

1.操作打文件makefile要加上宏

[code]

-D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE

[/code]

2.如果要make时打印警告信息，又不输出有符号和无符号比较、变量定义未使用警告

[code]

-Wall -Wno-sign-compare -Wno-unused-variable

[/code] 
