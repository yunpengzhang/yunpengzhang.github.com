---
author: admin
comments: true
date: 2012-09-04 13:39:51+00:00
layout: post
slug: several-functions-to-read-the-file
title: 读取文件的几个函数
wordpress_id: 497
categories:
- 博客相关
---

[http://man.chinaunix.net/develop/c&c++/linux_c/default.htm](http://man.chinaunix.net/develop/c&c++/linux_c/default.htm)

使用场景：一个文件，一行一个号码，读取并对号码进行操作。

在c语言里，可以使用fscanf，或fgets、getline配合strtoul进行操作。




> int fscanf(FILE * stream ,const char *format,....);

fscanf()会自参数stream的文件流中读取字符串，再根据参数format字符串来转换并格式化数据。格式转换形式请参考scanf()。转换后的结构存于对应的参数内。


调用的时候如果有非数字出现，会使程序卡住，一直在那读取。




> char * fgets(char * s,int size,FILE * stream);

fgets()用来从参数stream所指的文件内读入字符并存到参数s所指的内存空间，直到出现换行字符、读到文件尾或是已读了size-1个字符为止，最后会加上NULL作为字符串结束。


配合strtoul使用，如果有非数字，会转化为0，需要对0进行特殊判断。


> getline函数

#include <stdio.h>

ssize_t getline(char **lineptr, size_t *n, FILE *stream);

ssize_t getdelim(char **lineptr, size_t *n, int delim, FILE *stream);


如果你给定一个非空指针，和大小，如果不满足函数内部会relloc；如果是空指针，他会给你分配空间，不过要你自己释放。

配合strtoul使用，如果有非数字，会转化为0，需要对0进行特殊判断。

大多数时候使用fgets，用数组读指定的长度进行转换，有杂质也可以过滤过去。

strtoul的返回值可能是0或ULONG_MAX但是他们都会让文件和程序继续，对于号码不会有这样的值出现，只要过滤掉就好。


