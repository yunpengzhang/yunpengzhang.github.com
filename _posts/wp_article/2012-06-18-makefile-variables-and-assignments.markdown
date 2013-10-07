---
author: admin
comments: true
date: 2012-06-18 05:21:56+00:00
layout: post
slug: makefile-variables-and-assignments
title: makefile变量和赋值
wordpress_id: 400
categories:
- makefile
tags:
- make
- makefile
- 区别
- 大括号
- 括号
---

**makefile变量表示**

在makefile中，表示一个变量要用$(VAR) ${VAR}两种都行，但是在shell中必须要用大括号。其实makefile要求用小括号，只是在shell中解析用哪个都行。如果在makefile中要解析shell变量，要使用$${PWD}才可以。

**makefile赋值<!-- more -->**

VARIABLE = value

普通的赋值，如果使用值的时候会递归展开，并不是在定义的之后就把值赋好。
VARIABLE ?= value

只有在VARIABLE 为空的时候才赋值
VARIABLE := value

在声明的时候就把值给赋值好了，后面value的值改变不影响VARIABLE 。
VARIABLE += value

在VARIABLE 的值后面增加value的值。


> #example
a = orginal_value
b = $(a)
a = later_value
all:
    @echo $(b)
运行make
#make
later_value
#example
a = orginal_value
b := $(a)
a = later_value
all:
    @echo
$(b)
#make
original_value
