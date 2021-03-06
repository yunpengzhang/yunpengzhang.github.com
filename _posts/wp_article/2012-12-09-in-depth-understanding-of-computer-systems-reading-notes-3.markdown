---
author: admin
comments: true
date: 2012-12-09 09:22:15+00:00
layout: post
slug: in-depth-understanding-of-computer-systems-reading-notes-3
title: 深入理解计算机系统读书笔记3
wordpress_id: 604
categories:
- 读书笔记
tags:
- 深入理解计算机系统
- 读书笔记
---

从第三到第六章，对系统硬件层面讲述的比较多，于平时工作的范围联系不是很大，另外硬件方面的内容也需要再结合其他书籍再写笔记最好。所以只是把三到六章大概的读了以下，后面再补充详细的笔记。本次写的是第七章——链接。

现代操作系统抽象得最好的地方：程序执行时，就像自己单独使用所有的cpu，单独地占用系统的所有内存。使应用程序的开发变得简单，不必局限于底层的硬件类型来进行实现。

链接是将各种代码和数据部分收集起来，并组合成一个单一文件的过程。组合成单一的二进制bin文件，可以被加载到存储器执行。

<!-- more -->

链接可以在编译时，也可以在加载时执行，也可以在运行时由应用程序执行。

链接模块使模块开发成为可能，一个巨大的程序可以分为众多模块来分别编译，一部分修改后只要编译修改部分。

大多数编译系统提供编译驱动程序，用户在需要时调用语言预处理器、编译器、汇编器和链接器。分别把cpp->.i->.s->.o->可执行目标文件。

.o文件由不同的代码和数据节组成。

链接器要做两个事情：符号解析（把每个符号引用和定义联系起来）；重定位（把每个符号定义与一个存储器位置联系起来，然后修改这些符号的引用，使得他们指向这个存储器位置，从而重定位这些节）。

链接器只是链接，对目标机器了解很少，因为编译器和汇编器已经根据机器的硬件生成了代码和数据的指令，链接器只要处理好符号解析和重定位就可以了。

目标文件的三种形式：
可重定位目标文件。.o文件。包含二进制代码和数据，与其它可重定位目标文件可创建一个可执行目标文件。
可执行目标文件。也是包含二进制代码和数据，可以被直接拷贝到存储器并执行。 共享目标文件。可以加载或者运行时被动态加载到存储器并链接。.so文件。

可重定位目标文件结构：

[![](http://yunpengzhang.com/wp-content/uploads/2012/12/ELF可重定位目标文件-258x300.png)](http://yunpengzhang.com/wp-content/uploads/2012/12/ELF可重定位目标文件.png)

ELF头：包含了ELF文件的一些基本信息如头大小、目标文件类型、机器类型等。
.text：已编译程序的机器代码。
.rodata:只读数据
.data:已初始化的全局C变量。
.bss：未初始化的全局C变量。这个解不占据实际的空间，仅仅是一个占位符。
.symtab:符号表，存放程序定义和引用的函数和全局变量信息。和-g选项调试无关。不包括局部变量条目。
.rel.text：一个.text节中位置的列表。
.rel.data：被模块引用或定义的任何全局变量的重定位信息。
.debug：调试符号表。-g选项会有。
.line:程序行号和.text机器指令映射。
.strtab:包括.symtab和.debug节中的符号表，以及节头部中的节名字。都是以null结尾的字符串序列。

在函数中定义的局部static不是存储在栈中，也存在.data或.bss中。

可以利用static隐藏变量和函数名字，不被其他模块访问，通常做库的时候使用，防止别人的函数和你的重名。

链接器解析符号引用的方法是将每个引用与可重定位目标文件的符号表中的一个确定的符号定义联系起来。

[![](http://yunpengzhang.com/wp-content/uploads/2012/12/ELF可执行目标文件-300x206.png)](http://yunpengzhang.com/wp-content/uploads/2012/12/ELF可执行目标文件.png)

函数和已初始化的全局变量是强符号，未初始化的全局变量是弱符号。

链接器处理多重符号规则：
1.不允许有多个强符号。
2.如果有一个强符号和多个弱符号，选择强符号。
3.有多个弱符号，那么从弱符号任意选一个。

所以好的编程习惯很重要，尽量不要使用全局变量，如果使用，那么初始化。

静态库是一个打包的目标模块文件。

链接器如何使用静态库解析？
链接器从左到右，按照命令行出现顺序扫描目标文件和存档文件。维持一个可重定位目标文件的集合E，一个未解析的符号集合U，一个前面输入文件中已经定义的而符号集合D。初始都为空。
对于每个输入文件，如果是目标文件，那么链接器把f添加到E，修改U和D。如果是存档文件，那么尝试匹配U中未解析的符号和有存档文件成员定义的符号。如果存档文件成员m定义了一个符号来解析U中的引用，那么m加到E中，并修改U和D反映m中的符号和引用。对存档文件中所有目标文件反复进行这个过程，直到U和D都不再发生变化。如果链接器完成所有文件扫描后，U是空的，那么会合并和重定位E中的目标文件，构建执行文件。

所以对库的位置很重要，如果有相互依赖的，有的库要重新在makefile出现两次。

共享库是致力于解决静态库缺陷的一个现代创新产物。共享库是一个目标模块，运行时。可以加载到任意的存储器地址，并和一个在存储器中的程序链接起来。动态链接。
共享库也叫共享目标，unix为.so文件，windows为dll文件。

-fPIC指示编译器生成与位置无关的代码。-shared指示链接器创建一个共享的目标文件。

可以使用dlfcn.h中的dlopen，dlsym,dlclose,dlerror对共享库进行操作，应用程序。

ar,strings,strip,nm,size,readelf,objdump,ldd都是分析目标文件的工具。



**都为静态链接库,有同名函数参与情况下,链接会出现符号多次定义的错误!**

**若都为动态库,并且进行隐式调用,输出结果和动态库的顺序有关.**

**动态链接库如果不加库连选项 ,函数调用是正确的 加库路径,会以库的路径顺序为主! 左边覆盖右边. 而且当只链接其中一个时 也生效**

**在有静态和动态时，不把动态库库名显示加入编译选项,输出是正常的,如果加进去以静态库为主和link顺序无关！！**


## [深析静态链接库和动态链接库相同函数覆盖及库调用顺序问题](http://www.crazyshell.org/blog/?p=619)
