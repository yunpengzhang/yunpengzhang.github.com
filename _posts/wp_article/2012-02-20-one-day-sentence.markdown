---
author: admin
comments: true
date: 2012-02-20 07:43:43+00:00
layout: post
slug: one-day-sentence
title: 一天一句话
wordpress_id: 324
categories:
- 每日一句
---

2013-1-13



软件架构师是为技术团队服务而存在的，不是为其它的。

软件架构师是导师。

软件架构师是学生。

软件架构师是程序看门人。在狂欢过后留下来高兴的清扫场地。

软件架构师在混乱的地方维持次序，在有歧义的地方提出指导意见，在有争议的地方作出决断。

软件架构师参与系统的编码，对系统的核心有最彻底的理解。

软件架构师创造出能让整个公司内部有效交流的标准用语。

软件架构师读的代码要比他写的多——在系统变更完成前捕捉住bug。

软件架构师在不忽略当前的需求要求下提供产品技术上的远瞻。

软件架构师对错误勇于承认，对功绩不贪享。

软件架构师勇于承担工作，在工作完成时才心满意足。

爱是恒久忍耐 又有恩慈；爱是不嫉妒爱是不自夸 不张狂 不作害羞的事 不求自己的益处 不轻易发怒 不计算人的恶 不喜欢不义。只喜欢真理 凡事包容 凡事相信 凡事盼望 凡事忍耐爱是永不止息

20130104

/proc/net/udp

可以查看丢包

2013-1-2

**技术上不求甚解，业务上精益求精**。

20121231

解决“protobuf Encountered string containing invalid UTF-8”问题

参考http://code.google.com/p/protobuf/issues/detail?id=205

解决方法：使用-DNDEBUG选项编译程序。

20121224

宏定义：在文件中定义条件编译，然后生成.a，在makefile中再定义宏会打开吗？如果是动态库呢？
如果c文件在编译的时候已经通过#ifdef进行判断了，就无法通过自己的define去改变c库的功能了
通过.h来判断要define到哪个真正的实现

在linux进行非阻塞的socket接收数据时经常出现Resource temporarily unavailable，errno代码为11(EAGAIN)，这是什么意思？
这表明你在非阻塞模式下调用了阻塞操作。

使用udp多个进程在抢一个包的时候会出现recvfrom error。

20121213

C: assignment discards qualifiers from pointer target type

丢失了修饰符


这个warning说明，返回值是一个const的类型，而你声明使用的那个变量不是const，场景如下：
u_char *p = pcap_next(**, **);
因为pcap_next返回的是一个const u_char*，而这里使用的确实u_char*,因此编译器会告知你，这里有潜在的错误。


20121204

Gprof怎么测系统函数？

在编译是再加入“-lc_p”编译参数代替“-lc”编译参数，这样程序会链接libc_p.a库

但是有的开发机可能没有安装libc_p.a库。

20121201

三个月内用不到的东西，全删！




优柔寡断成因不敢为自己的决定负责。

我就负责了，错了没关系，我积累了经验这是人生经验和经历。

对爱人不能肆无忌惮。




20121130

    
    protoc: error while loading shared libraries: libprotobuf.so.7: cannot open shared object file: No such file or directory


_protobuf_的默认安装位置是 _/usr/local_，_/usr/local/lib_ 不在_Ubuntu_系统默认的 _LD_LIBRARY_PATH_ 里。这个问题 _protobuf_ 的 _README.txt_ 写得很清楚，怪自己不仔细。

1. 创建文件 _/etc/ld.so.conf.d/libprotobuf.conf_ 包含内容




    
    /usr/local/lib





2. 运行命令




    
    $ sudo ldconfig





20121124

常见算法错误之随机洗牌算法

http://bbs.bccn.net/thread-331122-1-1.html

20121122

screen：

https://wiki.archlinux.org/index.php/GNU_Screen_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)

http://en.gentoo-wiki.com/wiki/Screen

http://www.ibm.com/developerworks/cn/linux/l-cn-screen/

20121121

http://www.lupaworld.com/thread-29876-1-1.html








用gcc编译c程序的时候 经常会出现
implicit declaration of function '...' 的warning偶经过这几天的经验，发现主要有2种情况会产生这种warning  

1  没有把函数所在的c文件生成.o目标文件
2  在函数所在的c文件中定义了，但是没有在与之相关联的.h文件中声明

除了以上情况之外,据我所知至少还有另外的情况:所调用的函数的原型与所传的实参类型不匹配.比如下列代码,其头文件都声明过了,但是就是有警告,其编译选项为-O2 -Wall -fPIC -ldl -lusb:
semtimedop(idSem, &SemOp, 1, &strcTS);
但是如果加入原型声明就可以了:
int  semtimedop(int  semid, struct sembuf *sops, unsigned nsops, struct timespec *timeout);
以上在RedHat Linux10测试通过.值得一提的是,在Solaris10就没有这方面的问题.




svn diff 使用vimdiff查看的方法，看着很好，不过使用的时候发现还是不适合我，最终决定用colordiff工具。

http://www.cnblogs.com/xuxm2007/archive/2012/05/11/2496243.html

20121108

root下可以显示路径但是切换到其他用户时没有路径显示，很不方便。
在/etc/profile中加入


export PS1='\u@\h:\w>',其中\u显示当前用户账号，\h显示当前主机名，\W显示当前路径。然后source一下就搞定了




 


20121031

打败焦虑的最好办法，就是去做那些让你焦虑的事情。

20121027



	
  * 更多的选择不代表更多的自由；

	
  * 更多的选择导致决策的延迟和降低的满意感；

	
  * 快乐之秘诀，在于降低自己的期望值。


20121015

编程三要素：

检查参数正确性

变量初始化

函数返回值要检查

2012-10-14

成功者必是立即行动者。对于他们来讲，[时间](http://www.mifengtd.cn/articles/category/gtd)就是生命，时间就是效率，时间就是金钱，拖延一分钟，就浪费一分钟。

20121012

[http://bbs.chinaunix.net/thread-2011079-1-1.html](http://bbs.chinaunix.net/thread-2011079-1-1.html)

tcpdump -w test.pcap -i eth1 tcp port 6881

tcpdump -r test.pcap

<!-- more -->

20120807

这个命令很少有人用，但作为运维做代码部署，做一些程序的自动启动，可能会经常用到，如下：
su -c ls root 变更帐号为 root 并在执行 ls 指令后退出变回原使用者。

20120806

对新人心得：在没过需求的时候之前需要考虑需求实现的方法和难度，但不用设想的太多，因为有的需求可能有更好的方法实现，或者暂时不做，想的太多最后成了过度思考。可以想得多，但是不要投入太多资源。要想到最终目的是什么？业务运营更好，还是使用更多的技巧。

20120731

paste tmp1.txt tmp2.txt

awk 'NR==FNR{a[NR]=$1}NR>FNR{$2=a[FNR];print }' tmp1.txt tmp2.txt > tmp3.txt

2012-7-22

研究人员很早之前就知道，用户的网络活动主要被五大类需求所驱动：信息查找，人际沟通，自我表达，消磨时间和娱乐。

20120719

find . -name "*.c" -exec grep -q -s include {} \; -print

很好的一个方法

tar -zcf - xxx yyy | ssh  ip  "cd diry; tar -zxvf -"

传输文件办法

inet_ntoa等函数是有内部指针的，如果多个指针指向返回值会有问题。

2012-02-20

打印流水log是发现并恢复问题的最好方法之一。

2012-02-21

gcc以__i386__来 进行32位编码，而以__x86_64__来进行64位编码。

2012-02-22

明确需求再开发，先联调接口再进行各自开发才最优效率，初期想得多，后面少走弯路。

2012-02-23

竖排垂直标签就会吸引到顾客的注意——《超市长得一样一定有道理》

2012-03-06

问题：svn: Can't convert string from 'UTF-8' to native encoding

解决方法：export LC_ALL="en_US.UTF-8"

2012-03-07

pragma   pack(push,1)   ==   pragma   pack(1)

pragma   pack(pop)    和      pragma   pack() 作用是一样的

2012-03-14

vim 十六进制显示
:%!xxd
正常显示
:%!xxd -r

2012-05-16

cat /proc/sys/fs/file-max

查看最多打开文件数量
