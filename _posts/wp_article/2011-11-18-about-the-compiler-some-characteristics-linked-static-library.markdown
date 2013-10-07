---
author: illidan
comments: true
date: 2011-11-18 09:52:25+00:00
layout: post
slug: about-the-compiler-some-characteristics-linked-static-library
title: 关于编译器链接静态库的一些特性
wordpress_id: 279
categories:
- 开发知识
---

有这样一个工程目录：




-test/




-dir1/




libtest.a




test.cpp




test.h




test.o




-dir2/




libtest.a




test.cpp




test.h




test.o




main.cpp




Makefile




 




其中，dir1的test.cpp和test.h中定义了func_a和func_b：

















#include <iostream>




using namespace std;




 




#include "test.h"




 




void func_a(void)




{




cout << "a" << endl;




}




 




void func_b(void)




{




cout << "b" << endl;




}








#pragma once




 




void func_a(void);




void func_b(void);












 




dir2的test.cpp和test.h中只定义了func_a：

















#include <iostream>




using namespace std;




 




#include "test.h"




 




void func_a(void)




{




cout << "a" << endl;




}








#pragma once




 




void func_a(void);












 




main.cpp调用了func_a和func_b：




#include <iostream>




using namespace std;




 




#include "test.h"




 




int main(void)




{




func_a();




func_b();




 




return 0;




}




 




我们列出下面几种方式编译main，Makefile和编译结果如下：




#dir1的静态库在前，dir2的静态库在后（ok）




main1: main.cpp




g++ -g -Wall -I./dir1 -I./dir2 -o $@ $^ -L./dir1 -ltest -L./dir2
-ltest




 




#dir2的静态库在前，dir1的静态库在后（fail）




main2: main.cpp




g++ -g -Wall -I./dir1 -I./dir2 -o $@ $^ -L./dir2 -ltest -L./dir1
-ltest




#编译结果：main.cpp:9: undefined
reference to `func_b()'




 




#dir1的.o在前，dir2的.o在后（fail）




main3: main.cpp




g++ -g -Wall -I./dir1 -I./dir2 -o $@ $^ ./dir1/test.o ./dir2/test.o




#编译结果：dir2/test.cpp:6: multiple
definition of `func_a()'




 




#dir2的.o在前，dir1的.o在后（fail）




main4: main.cpp




g++ -g -Wall -I./dir1 -I./dir2 -o $@ $^ ./dir2/test.o ./dir1/test.o




#编译结果：dir1/test.cpp:6: multiple
definition of `func_a()'




 




#dir2的.o在前，dir1的静态库在后（fail）




main5: main.cpp




g++ -g -Wall -I./dir1 -I./dir2 -o $@ $^ ./dir2/test.o -L./dir1
-ltest 




#编译结果：dir1/test.cpp:6: multiple
definition of `func_a()'




 




#dir1的.o在前，dir2的静态库在后（ok）




main6: main.cpp




g++ -g -Wall -I./dir1 -I./dir2 -o $@ $^ ./dir1/test.o -L./dir2
-ltest 




 




clean:




rm -f main[0-9]




 




原因好像是这样的：




编译器链接多个.o时，没有顺序的概念。




但是链接.a时，为了节省工作量，




（1）只链接需要用到的（摘抄一段说明：“传统的Unix编译环境下，静态库的加载是顺序搜索一遍，遇到没链接的函数就记下，如果这个函数在后面的库或obj中出现，链接器就会把这个函数所在的obj链接过去,，不包含未链接函数的obj就会被忽略过去(静态库也只是obj的简单打包而已)。于是, “-lc”的时候, 因为那两个函数在main.c中都没有被调用，链接器就把a.o略过去了，“-ld”的时候虽然有了a.o中函数的调用，但链接器已经把它给略过去, 找不到了。）；




（2）相同名字的.o只用第一次出现的。
