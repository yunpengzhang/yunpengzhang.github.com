---
author: admin
comments: true
date: 2013-05-27 12:59:01+00:00
layout: post
slug: random-port-multiplexing-and-packet-loss-problems-and-solutions
title: 随机端口复用导致丢包的问题及解决方法
wordpress_id: 644
categories:
- 开发运营
tags:
- linux
- 端口
- 端口复用
- 绑定
- 随机绑定
---

因为redhad随机分配端口和suse策略不同，导致有随机分配端口的业务迁移到redhad上会

出现端口被抢占而丢包的问题。

**解决方法：**

**【方法一】**创建socket的时候，setsockopt函数设置绑定端口socket的属性为不可重用的。

**【方法二】**控制绑定端口范围在32768以下。

**0.****抢占的原则是什么？**

**      **   顺序和系统实现有关，我们公司的suse和redhad都是后启动绑定的socket抢占先绑定的。

**1.****为什么会出现tlinux上而suse没事？**

因为suse随机绑定端口时会绕过已经被绑定的端口，而tlinux不会，会完全

随机。但是已经绑定的端口如果设置成not reuse，就不会被绑定。

**2.****都什么程序会出现这种问题？**

有随机绑定端口，而且设置成reuse的都可能会遇到。

例如：

如果初始化函数地址填0，会随机绑定端口。

一些老的agent进程也是随机创建端口，而且也是允许reuse。

一些程序内部的api会随机创建端口发包收取。

**3.****为什么要缩小绑定端口范围在32768以下？**

**     **    因为系统的配置随机范围在32768——61000范围。


> sysctl -a | grep ip_local_port_range
net.ipv4.ip_local_port_range = 32768 61000


我们的老库创建socket函数，框架都设置udp是socket在创建时是reuse的，

所以迁移tlinux都会出现这些问题。

对于udp，不用设置reuse，因为只有多播的时候才需要，但是我们的

业务并不使用多播。可能是框架和库出于对tcp的统一，都设置成了reuse。

深度参考

http://blog.csdn.net/dog250/article/details/5303572

vi /etc/sysctl.conf
添加下面一行：
net.ipv4.ip_local_port_range = 1024 65535

然后执行：

sysctl -p

意思是告诉linux可以使用的端口是从1024到65535，不用编译内核就可以生效。
[root@PerfTestApp3 ~]# sysctl -a|grep ip_local_port_range
net.ipv4.ip_local_port_range = 32768 61000

http://rdc.taobao.com/blog/cs/?p=1195
SO_REUSEADDR可以用在以下四种情况下。
(摘自《Unix网络编程》卷一，即UNPv1)
1、当有一个有相同本地地址和端口的socket1处于TIME_WAIT状态时，而你启
动的程序的socket2要占用该地址和端口，你的程序就要用到该选项。
2、SO_REUSEADDR允许同一port上启动同一服务器的多个实例(多个进程)。但
每个实例绑定的IP地址是不能相同的。在有多块网卡或用IP Alias技术的机器可
以测试这种情况。
3、SO_REUSEADDR允许单个进程绑定相同的端口到多个socket上，但每个soc
ket绑定的ip地址不同。这和2很相似，区别请看UNPv1。
4、SO_REUSEADDR允许完全相同的地址和端口的重复绑定。但这只用于UDP的
多播，不用于TCP。
