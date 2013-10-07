---
author: admin
comments: true
date: 2012-12-29 13:23:24+00:00
layout: post
slug: resolve-memory-access-conflicts
title: 解决内存读写冲突
wordpress_id: 625
categories:
- 开发运营
tags:
- cas
- compare and set
- fetch and add
- GCC
- 多cpu
- 多进程
- 读写冲突
---

最近遇到了几个内存读写冲突的场景：

1.内存hash读写冲突

一个进程写，多个进程读，当读进程找到要读取的指针时，写进程进行了写操作，然后回收了原先读进程要读的内存块。这时cpu又调度到读进程，读失败，返回错误给前端，重试ok。

由于业务场景可以容忍读到旧数据，最终采用的解决方法为延迟回收内存块，即内存回收前放到一个回收队列里，先不清理数据，过500ms后再回收数据，有重组的时间等待读冲突的读进程取数据。

<!-- more -->

但是要不能容忍读到旧数据呢？加锁，读的时候加锁，读写互斥，我觉得不好，当读量巨大一直有读，让写永远也抢不到锁，最终活锁。

只有写的时候加锁，读发现读到的有问题然后重新读指针读新数据。

2.上报内存多进程写冲突

多个进程收到请求，对统一上报属性值进行累加，操作的是同一块固定共享内存。发现请求量大时上报数量不准。在t1时刻，进程p1读到内存值v1，然后cpu调度到p2，也读到内存值v1。之后p1把v1+x，然后set到内存，这时p2也把v1+y，然后set到内存。结果覆盖了。少报了x。

解决方法:都一直是累加操作，应该让每个进程读取和累加完成之后再被cpu调度走。即读取和累加成为一个原子操作。

在gcc中有原子函数接口。

type` __sync_fetch_and_add (`type` *ptr, `type` value, ...)`

使用后就好了。

3.多进程写操作hash内存检测节点

对于场景2来说，数据结构是数组，每个上报在固定的共享内存中。但是如果改为hash呢，会有什么不同吗？每个进程除了要把内存的值原子读取和操作外，还要对操作的hash节点的指针进行校对，因为你把一个属性的指针给删除或修改了，其他的进程可能正保存着呢，然后进行读写操作会把其他内存写如脏数据，也使自己的上报不准。

解决方法：使用cas（Compare-and-swap），就是在修改值之前先把值读出来，然后写之前会比对刚才读到的值和现在写之前内存的值是否一致，如果一致才进行写操作，把新的值set进去。

在gcc中的函数是

`bool` `__sync_bool_compare_and_swap (type *ptr, type oldval type newval, ...)`


`type __sync_val_compare_and_swap (type *ptr, type oldval type newval, ...)`





以上三个场景究其原因，都是cpu是多核，多个进程同时操作，cpu抢占所造成的。在多线程编程中会更多见。

都是因为读写操作的时序问题，把一个本应该是原子操作的整个事务给分开提交造成的冲突。



扩展阅读：
http://blog.csdn.net/lifesider/article/details/6582338
cas （compare and swap）http://blog.csdn.net/chenziwen/article/details/6050140
Built-in functions for atomic memory accesshttp://gcc.gnu.org/onlinedocs/gcc-4.1.1/gcc/Atomic-Builtins.html

http://en.wikipedia.org/wiki/Compare-and-swap

http://en.wikipedia.org/wiki/Fetch-and-add
