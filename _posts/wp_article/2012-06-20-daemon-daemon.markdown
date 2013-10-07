---
author: admin
comments: true
date: 2012-06-20 06:08:56+00:00
layout: post
slug: daemon-daemon
title: daemon守护进程
wordpress_id: 420
categories:
- 开发知识
tags:
- C++
- daemon
- linux
- 守护进程
---

**　　什么是守护进程？**

守护进程（daemon）是运行在后台不属于任何终端的进程。服务类程序多为daemon进程：例如网络程序。而且多采用named的形式命名。

如何创建守护进程？

1.程序在后台执行。

fork（）进程，然后父进程退出。新的子进程脱离命令行或shell程序的控制，在后台运行，并且不再是进程组组长，为下一步setsid做准备。

2.脱离控制终端，登录会话和进程组。

进程与控制终端，登录会话和进程组之间的关系：进程属于一个进程组，进程组号（GID）就是进程组长的PID。登录会话可以包含多个进程组。他们用一个控制终端，这个终端通常是创建进程的登录终端。

<!-- more -->

控制终端，登录会话和进程组通常是从父进程继承下来的。要摆脱他们，要调用setsid（）邓伟会话组长。

setsid（）如果是进程组长调用会失败，但是第一步fork后子进程不是会话组长。调用成功后，进程成为新的组长，与原来登录会话和进程组脱离，由于会话过程对控制终端的独占性，进程同事与控制

终端脱离。

3.禁止进程重新打开控制终端。

进程成为无终端的会话组长，是可以重新申请打开一个控制终端，可以再次fork是进程不成为会话组长来禁止重新打开控制终端。

再次fork，新的子进程来执行程序。

4.关闭打开文件描述符，然后chdir("/")，更改目录为根目录。

关闭从父进程继承的文件描述符，防止浪费资源和无法卸载文件系统等问题。

更换当前工作目录保证进程所在文件系统卸载后不会影响到进程正常工作。

5.重设文件掩码（可选）

可以对进程的文件进行所有操作。

umask(0)

6.处理SIGCHLD信号（可选）

如果服务器请求来时生成子进程处理请求。如果父进程不等待子进程结束，会产生僵尸进程，占用资源。但是等待子进程结束，会增加父进程负担，影响并发性能。可以使用signal(SIGCHLD,SIG_IGN);

内核在子进程结束时不会产生僵尸进程。

**　　例子程序**

    
    int main(void)
    {
    /*如果本进程是从前台作为一个shell命令启动的，当父进程终止时，shell就认为该命令已经执行完毕。这样子进程就自动在后台运行。*/
    if ((pid =Fork())<0)
    return -1;
    else if (pid)
    exit(0); //parent terminates
    //child continues
    /*当前进程编程新的会话的头进程以及新的进程组的进程组头进程，从而不再有控制终端。*/
    if (setsid()<0) //become session leader
    return -1;
    /*这里必须忽略SIGHUP信号，因为当会话头进程终止时，其会话中的所有进程（即再次fork产生的子进程）都会受到SIGHUP信号。*/
    Signal(SIGHUP, SIG_IGN);
    /*再次fork的目的是确保本守护进程将来即使打开了一个终端设备，也不会自动获得控制终端。
    当没有控制终端的一个会话头进程打开一个终端设备时，该终端自动成为这个会话头进程的控制终端。*/
    if ((pid = Fork())<0)
    return -1;
    else if(pid)
    exit(0); //child 1 terminates
    //child 2 continues
    /*要是守护进程产生了某个core文件，该文件就存放在当前目录中。
    改变工作目录的另一个理由是，守护进程可能是在某个任意的文件系统中启动，如果仍然在其中，那么该文件系统就无法拆卸，除非使用潜在破坏性的强制措施。*/
    chdir("/"); //change working directory
    /*要是一个守护进程未打开这些描述子，却作为服务器打开了某个客户关联的一个套接口，那么这个套接口很可能占用这些描述字（譬如标准输出1、标准错误输出2），
    这种情况下如果守护进程调用诸如perror之类函数，那就会把非预期的数据发送给那个客户。*/
    for (i = 0; i < MAXFD; i++) //close off file descriptors
    close(i);
    //redirect stdin, stdout, and stderr to /dev/null
    open("/dev/null", O_RDONLY);
    open("/dev/null", O_RDWR);
    open("/dev/null", O_RDWR);
    openlog(pname LOG_PID, facility);
    return 0;
    }


**　　备忘**

可以使用nohup在后台启动程序不受终端干扰

尽量在调用daemon_init之前不要创建socket或打开文件。

**　　参考**

[1.7 How do I get my program to act like a daemon?
](http://www.steve.org.uk/Reference/Unix/faq_2.html)
[Linux 守护进程的编程方法
](http://www.360doc.com/content/11/0111/15/1317564_85756949.shtml)
