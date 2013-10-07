---
author: illidan
comments: true
date: 2011-10-21 03:55:33+00:00
layout: post
slug: hardware-related-secure-random-number-dev-random-and-dev-urandom
title: '硬件相关的安全随机数：/dev/random和/dev/urandom '
wordpress_id: 202
categories:
- 开发运营
tags:
- random
- urandom
---

rand()函数只要种子相同，生成的随机队列就是固定的。虽然我们常用系统时间当作种子，以保证不同时刻生成的随机队列是不同的。但是这种随机队列有个弊端：一旦猜中种子，就可以知道过去、现在和未来的随机数是什么，从而很容易被破解。


 

The  character  special  files /dev/random and /dev/urandom (present since Linux 1.3.30) provide an interface to the kernel's random number generator.

/dev/random和/dev/urandom是根据机器硬件的一些动态值生成的随机字符串文件（The  random number generator gathers environmental noise from device drivers and other sources into an entropy pool.），比较难破解。

 

/dev/urandom生成的速度比/dev/random快。如果不能立即生成随机串，/dev/random会一直阻塞，有时会非常耗费CPU；/dev/urandom则会根据其他值立即生成一个随机串，不会阻塞。/dev/urandom生成的随机值没有/dev/random随机。大多数情况下，我们选用/dev/urandom。
<!-- more -->
 

获取urandom buf的例子：
`	int GetURandBuf(char * sBuf, int iLen)
	{
	        static int iFd = -1; //这个的好处是不用每次都open
	        if (0 > iFd)
	        {
	                iFd = open("/dev/urandom", O_RDONLY);
	                if(0 > iFd)
	                {
	                        return -1;
	                }
	        }
	        if(read(iFd, sBuf, iLen) != iLen)
	        {
	                close(iFd);
	                iFd = -1;
	                return -1;
	        }
	        return 0;
	}`
取到随机buf后如何再利用这个buf做其他花样就随意了。
