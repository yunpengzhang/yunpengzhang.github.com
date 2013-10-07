---
author: admin
comments: true
date: 2012-10-13 15:15:24+00:00
layout: post
slug: vigilance-the-function-is-defined-or-declared-before-being-called
title: 警惕：函数被定义或声明前被调用
wordpress_id: 522
categories:
- c/c++
tags:
- C C语言 陷阱与缺陷
---

如果一个函数在被定义或声明前被调用，那么返回类型就默认为整型！有时会产生预想不到的结果，而且很难发现错误。

在编译C程序时要加如-Wall选项，不要放过一个warning，调用的函数要引用相应的头文件，在头文件中有函数声明。

最好是加上-Werror选项，要保持代码没warning。

<!-- more -->

cal.h内容

    
    #ifndef _CAL_H_
    #define _CAL_H_
    #include <stdio.h>
    #include <stdint.h>
    
    uint64_t calfunc(uint64_t x);
    #endif


cal.c内容

    
    #include <stdio.h>
    #include <stdio.h>
    #include "cal.h"
    
    uint64_t calfunc(uint64_t x)
    {
    	uint64_t ret = x * 1000000;
    	return ret;
    }


main.c

    
    #include <stdio.h>
    #include <stdint.h>
    /*#include "cal.h"*/
    
    int main(int argc, char **argv)
    {
    	uint64_t x = 12345678912345;
    	uint64_t ret = calfunc(x);
    	printf("ret = %llu\n", ret);
    	return 0;
    }


如果包含cal.h文件，执行正常，打印

ret = 12345678912345000000

否则打印

ret = 18446744071584794688

因为0xab54a98f815ac840在返回的时候当作int返回，被截断然后复制为0xffffffff815ac840
