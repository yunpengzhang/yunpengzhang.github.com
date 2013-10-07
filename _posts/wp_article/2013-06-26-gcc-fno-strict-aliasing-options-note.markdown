---
author: admin
comments: true
date: 2013-06-26 09:53:13+00:00
layout: post
slug: gcc-fno-strict-aliasing-options-note
title: gcc -fno-strict-aliasing 选项注意
wordpress_id: 692
categories:
- 开发运营
tags:
- -fno-strict-aliasing
- GCC
- 优化
- 编译
---

最近在做CRO的时候发现好多makefile都使用了“-fno-strict-aliasing”选项，仔细查看了一下他的含义，发现在使用优化的时候，大家应该多注意。




> 

> 
> 

If optimization level is >= 2 in gcc-4.1, strict-aliasing is used, and this could cause probelms when a pointer is referencing to a different type of object and the object is refered there after by using this pointer. That is the case in this example. So you should force the compiler to not use strict-aliasing by a argument "-fno-strict-aliasing" if you want to use "-O2" or "-O3".









在gcc4.1版本以上，如果多种类型的指针指向同一块内存，使用-O2或-O3优化时，会把不同类型指针的操作给优化掉，认为不会有不同类型的指针操作同一块内存。






举个例子

    
    #include <sys/socket.h>
    #include <arpa/inet.h>
    #include <sys/time.h>
    #include 
    
    int n;
    int foo(unsigned long *ptr) {        
        n=1;
        *ptr=3;
        return n;
    }
    int main() {
        fprintf(stdout, "%d\n", foo(&n));
        return 0;
    }




代码编译




    
     gcc main.c && ./a.out
    main.c: In function 'main':
    main.c:15: warning: passing argument 1 of 'foo' from incompatible pointer type
    main.c:9: note: expected 'long unsigned int *' but argument is of type 'int *'
    3





如果优化




    
     gcc -O2 main.c && ./a.out
    main.c: In function 'main':
    main.c:15: warning: passing argument 1 of 'foo' from incompatible pointer type
    main.c:9: note: expected 'long unsigned int *' but argument is of type 'int *'
    1





加入参数




    
     gcc -O2 -fno-strict-aliasing main.c && ./a.out
    main.c: In function 'main':
    main.c:15: warning: passing argument 1 of 'foo' from incompatible pointer type
    main.c:9: note: expected 'long unsigned int *' but argument is of type 'int *'
    3





综上，得出结论：

1.重视编译告警，根据告警修复代码。

2.编译优化是否真的重要，对性能提升是否明显，如果不需要尽量不要提早优化，gcc的优化参数很多，有些坑没踩过是不会hold住的。

3.尽量减少不同类型指针强转的操作，可以使用同类型指针或者union操作等方法。

4.编译优化加上“-fno-strict-aliasing”选项，让编译器不优化这里。

http://www.dutor.net/index.php/2012/07/gcc-strict-aliasing/

http://www.zeali.net/entry/454

[http://hi.baidu.com/rsjdsibkwtnrxyr/item/b04e86dbdc8351de241f40cc](http://hi.baidu.com/rsjdsibkwtnrxyr/item/b04e86dbdc8351de241f40cc)


