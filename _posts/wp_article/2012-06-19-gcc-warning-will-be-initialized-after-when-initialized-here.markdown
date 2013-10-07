---
author: admin
comments: true
date: 2012-06-19 12:26:39+00:00
layout: post
slug: gcc-warning-will-be-initialized-after-when-initialized-here
title: gcc warning “will be initialized after/when initialized here”
wordpress_id: 414
categories:
- 程序设计
tags:
- C++
- G++
- GCC
- Warning
---

下面这段代码会产生warning

    
    #include <iostream>
    using namespace std;
    
    struct foo
    {
        foo() : Y(1), X(Y+1) {}
        int X;
        int Y;
    };
    
    int main(int argc, char *argv[])
    {
        return 0;
    }


 <!-- more -->


> In constructor 'foo::foo()':
8: warning: 'foo::Y' will be initialized after
7: warning:   'int foo::X'
6: warning:   when initialized here


产生这个warning的原因是因为初始化列表的顺序和声明变量的顺序不一致。

编译器要求必须是一致的，否则会有问题。

上面的例子，X的值最开始初始化为Y+1,由于Y的值是垃圾值，所以X的值也是垃圾值，然后Y被初始化为1。

如果修改X和Y的声明顺序，则Y初始为1，X初始为2。

切记：

编译的时候带上-Wall选项！

不要放过一个warning！

在使用初始化列表的时候顺序要和声明一致！
