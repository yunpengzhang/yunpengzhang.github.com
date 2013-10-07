---
author: admin
comments: true
date: 2012-06-24 14:08:59+00:00
layout: post
slug: linux-memory-allocation-precautions
title: linux内存分配注意事项
wordpress_id: 439
categories:
- 开发知识
tags:
- C++
- linux
- 内存分配
---

在开发过程中，分配内存是家常便饭，然而其中也有很多“潜规则”需要注意。

linux栈内存不要超过10M，超过会使程序崩溃，而且还不容易发现。在做应用程序时经常遇到这种情况，大家不会吝惜内存分配，可能结构体套结构体，一会就把局部内存给分满了。对于这种情况，可以分配静态变量。但是最根本的还是按需分配，对于程序要使用的内存大小和结构设计要了如指掌。<!-- more -->

在程序中，不用频繁分配释放大内存，例如在一个循环里不停地分配然后执行完回收。

linux分配内存有两个系统调用：brk和mmap。当分配的内存小于128k时（M_MMAP_THRESHOLD控制），在.data段的_edata指针往高地址便宜相应大小，完成内存分配。如果最高地址的内存回收，并不是马上指针指向低地址紧缩，而是空闲内存达到128k（M_TRIM_THRESHOLD控制）。禁止内存紧缩也会提高程序的执行效率。可以调用以下代码实现。

    
    mallopt(M_TRIM_THRESHOLD, -1); // 禁止内存紧缩


如果分配的内存大于128k时，会调用mmap在堆和栈的中间分配虚拟内存，并且分配的内存可以单独释放，不想brk分配的内存要高地址的内存释放后才能释放。用mmap调用的会进入内核态，引起缺页中断，对应用程序的效率产生影响。

可以分配静态内存来避免mmap调用，或者开始分配好一块内存减少频繁分配释放，共享内存也是一种解决方法。

在程序中调用下面代码可以不使用mmap分配内存。

    
    mallopt(M_MMAP_MAX, 0); // 禁止malloc调用mmap分配内存


参考《[频繁分配释放内存导致的性能问题的分析](http://blog.csdn.net/sniperhuangwei/article/details/5422016)》

简单的测试程序

    
    #include <stdio.h>
    #include <stdlib.h>
    #include <unistd.h>
    
    int main(int argc, char **argv)
    {
        for (int i = 0; i < 150; ++i)
        {
            char *ptr = (char *)malloc(1024 * i);
        }
        return 0;
    }


测试程序运行用strce查看

    
    brk(0xca4000)                           = 0xca4000
    brk(0xce2000)                           = 0xce2000
    brk(0xd21000)                           = 0xd21000
    brk(0xd61000)                           = 0xd61000
    mmap(NULL, 135168, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f1a9efac000
    mmap(NULL, 135168, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f1a9ef8b000
    mmap(NULL, 135168, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f1a9ef6a000


申请的内存达到135168大约100多k的时候调用mmap申请。

至于为什么不是128k还没有找到原因，有知道的朋友可以帮忙解答下。



**用命令ps -o majflt minflt -C program来查看进程的majflt, minflt的值，这两个值都是累加值，从进程启动开始累加。在对高性能要求的程序做压力测试的时候，我们可以多关注一下这两个值。 **

参考http://bbs.csdn.net/topics/330179712


### [百度分享]频繁分配释放内存导致的性能问题的分析
