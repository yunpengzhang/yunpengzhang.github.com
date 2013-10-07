---
author: admin
comments: true
date: 2012-05-02 13:07:44+00:00
layout: post
slug: improve-efficiency-use-awk-instead-of-reading-a-file-shell
title: 提高效率——使用awk代替shell读文件
wordpress_id: 342
categories:
- shell
tags:
- shell awk
---

对一个号码文件进行操作，调用的处理事件越来越慢。

曲线如下

[![](http://yunpengzhang.com/wp-content/uploads/2012/05/image001-300x164.jpg)](http://yunpengzhang.com/wp-content/uploads/2012/05/image001.jpg)

脚本大致如下：

#!/bin/bash

while read seq

do

echo $seq

done < seq.dat<!-- more -->

改成awk读取后发现曲线平稳

[![](http://yunpengzhang.com/wp-content/uploads/2012/05/image002-300x141.jpg)](http://yunpengzhang.com/wp-content/uploads/2012/05/image002.jpg)

脚本如下

#!/bin/bash

awk '{system("echo " $0);}' seq.dat

shell利用重定向read时的系统调用如下：

[![](http://yunpengzhang.com/wp-content/uploads/2012/05/image003-300x131.jpg)](http://yunpengzhang.com/wp-content/uploads/2012/05/image003.jpg)

在不停地调用_llseek定位文件偏移，然后读取128个字节，使用read需要的字节数后，执行输出操作，然后再重复_llseek的操作。每次读取的字节是有重复的。随着文件的增加，只有_llseek的参数变大，**推断出因为****_llseek****系统调用与文件便宜有关，但还不确定，因为并未读取源码，请知道的同学解答。**

Awk操作如下：

[![](http://yunpengzhang.com/wp-content/uploads/2012/05/image005-300x92.jpg)](http://yunpengzhang.com/wp-content/uploads/2012/05/image005.jpg)

采用缓存，操作一次读一行，使用完缓冲区顺序往下读，顺序读取，所以执行稳定。




**建议：以后对号码文件调用工具进行操作，使用awk****调用shell****的方法会得到比较稳定的速度，在预期的时间内完成任务。**

**附：****测试程序**

seq 1000000 > seq.dat

while read s;

do

if [ `expr $s % 50000` -eq 0 ];then

echo -n $( expr `date +%s` - $A) ' ';

A=`date +%s`;

fi

done < seq.dat


