---
author: admin
comments: true
date: 2012-06-30 13:40:51+00:00
layout: post
slug: vim-tips-merger-of-two-of-the-tail
title: vim技巧之两段的尾合并
wordpress_id: 449
categories:
- vim
tags:
- vim
- 技巧
---

在水木上看到的


> aaa
bbb
ccc
111
222
333


合并成


> 
aaa 111
bbb 222
ccc 333


光标放到ccc行

    
    :1,g/^/''+m. | -j


还不明白原理，有时间查下资料解释下。
