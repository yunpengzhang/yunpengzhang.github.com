---
author: admin
comments: true
date: 2012-10-13 13:11:40+00:00
layout: post
slug: c-language-using-inline-functions
title: C语言使用inline函数
wordpress_id: 508
categories:
- c/c++
tags:
- c c语言 gcc inline
---

在C语言中定义inline函数，如果有多个文件包含并在一起用gcc编译会出现重复定义的错误，但是使用g++则不会。先上代码展示问题。<!-- more -->

在inline.c和inlin.h中定义usefunc函数，调用了inline的func函数，相当于写了一个库函数，供main.c调用。

    
    #ifndef _CINLINE_H_
    #define _CINLINE_H_
    #include <stdio.h>
    int usefunc(int x);
    
    //static inline int func(int x)
    inline int func(int x)
    {
    	printf("in func x = %d\n", x);
    	return 0;
    }
    #endif



    
    #include <stdio.h>
    #include "cinline.h"
    int usefunc(int x)
    {
    	printf("in usefunc x=%d\n", x);
    	func(x);
    	return 0;
    }



    
    #include <stdio.h>
    #include "cinline.h"
    int main(int argc, char **argv)
    {
    	usefunc(10);
    	return 0;
    }


编译函数结果报错显示重复定义。

    
    multiple definition of `func'
    main.o:cinline.h:6: first defined here
    collect2: ld returned 1 exit status
    make: *** [main] 错误 1


修改方法：

1.把inlin.h中的inline改为static函数即编译正常显示结果

2.改用g++进行编译


> in usefunc x=10

in func x = 10


为什么g++可以而gcc不行？

因为g++和gcc对inline的支持不一样，在g++中是按照C++的标准来支持inline函数的：“直接嵌套汇编代码，取代函数调用，提高效率。”

而gcc对inline的支持则有不同。

**c99标准**


inline ：用于同一c/cpp文件内部被调用处展开；对外部文件来说函数不可用 static inline ：用于在同一c/cpp文件内部被调用处展开；一般情况下，编译器并不会为此函数生成单独的目标代码；如遇到内联函数无法展开，或内联函数以地址形式被调用，则编译器将会为此内联函数生成单独的代码；


简单地说，c99中inline关键字申明的函数一般仅用于同一文件，函数本身不会生成单独的目标代码；static关键字修订后，如果需要，则会生成单独的目标代码。

**gcc**


inline ：对同一c/cpp文件，函数将会在被调用处展开；对外部文件，此函数等同于"extern"函数 static inline ：与c99标准中相同 extern inline : 仅用于同一c/c++文件内部，在被调用处展开


gcc中的inline关键字与c99中不同，默认情况下(仅使用inline)，在同一文件中被调用处当作内联函数展开，而在外部文件调用中等同于普通extern函数(也就是说会生成单独的目标代码)；加static关键字修订后，反而不可应用于外部文件，但如果需要可以生成单独的目标代码；gcc扩展的extern inline模式更是缩小函数的使用仅限于在同文件中展开。

gcc对C语言的inline做了自己的扩展，其行为与C99标准中的inline有较大的不同。

相对于C99的inline来说，GCC的inline更容易理解：可以认为它是一个普通全局函数加上了inline的属性。即在其定义所在文件内，它的 表现和static inline一致：在能展开的时候会被内联展开编译。但是为了能够在文件外调用它，gcc一定会为它生成一份独立的汇编码，以便在外部进行调用。即从文件 外部看来，它和一个普通的extern的函数无异。

在main.c引用头文件的时候，相当与在头文件中定义了一个extern的函数，所以会报错。

在c语言里就不要把函数定义到头文件中了。

使用static就使函数分别在被包含的文件范围内可见。

在写程序时，尤其是写库，把外部用不到的都加上static内部自己用，宏定义也要有前缀，减少重复定义的危险。

参考：

[c语言的inline](http://news.ccidnet.com/art/32859/20100712/2112675_1.html)

[Why the inline function can not be covered?](http://www.cppblog.com/codejie/archive/2012/09/21/191508.html)

[Inline Functions In C](http://www.greenend.org.uk/rjk/tech/inline.html)


## 


makefile

    
    CXX = g++
    CC = gcc
    
    TARGET = main
    C_FLAGS += -g -Wall
    LIB_FLAGS = -pthread
    # INC += .
    # LIB += 
    
    all: $(TARGET)
    
    main: main.o cinline.o
    	$(CC) -o $@ $^ $(LIB_FLAGS) $(LIB) $(C_FLAGS)
    
    .cpp.o:
    	$(CXX) -c -o $*.o $(INC) $(C_FLAGS) $*.cpp
    .c.o:
    	$(CC) -c -o $*.o $(INC) $(C_FLAGS) $*.c
    clean:
    	-rm -f *.o $(TARGET)
