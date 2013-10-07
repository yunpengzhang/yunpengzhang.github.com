---
author: admin
comments: true
date: 2012-06-20 12:00:22+00:00
layout: post
slug: daemon-running-single-case
title: 守护进程单例运行
wordpress_id: 429
categories:
- 开发知识
tags:
- daemon
- fcntl
- lock
- 文件锁
---

**如何让守护进程单例运行？**

如果已经启动一个进程，再启动的进程要监控到已启动进程的状态，涉及到进程间通信。
如果是网络程序，可以不重用端口，发现已经绑定就不创建。
可以申请公共的共享内存，在共享内存中标记已经有进程启动，退出时标记回来。
但是上面的方法有问题，大家可以想一下。
常用的方法是建立一个进程文件，用文件锁锁住文件。再启动的进程发现文件锁住了，就不启动，保证进程单例运行。

**为什么要单例运行？**
防止两个进程使用统一资源，调度相同的逻辑引起混乱。例如crontab，两个一起调用会让任务重复执行。

<!-- more -->

**什么是文件锁？**
进程创建一个文件，给文件添加一把锁，只允许创建一把写锁。其他进程无法操作文件，意味着已经存活着相同的进程。当创建锁的进程终止时，锁被解除。

实现锁的方法

    
    fcntl()、lockf()、flock()
    int fcntl (int fd, int cmd, struct flock *lock);



    
    struct flock {
    	...
    		short l_type;    /* Type of lock: F_RDLCK,
    				    F_WRLCK, F_UNLCK */
    	short l_whence;  /* How to interpret l_start:
    			    SEEK_SET, SEEK_CUR, SEEK_END */
    	off_t l_start;   /* Starting offset for lock */
    	off_t l_len;     /* Number of bytes to lock */
    	pid_t l_pid;     /* PID of process blocking our lock
    			    (F_GETLK only) */
    	...
    };


返回errno，如果为EAGAIN或者EACCES，表示其他进程已经拥有该文件的锁，本次操作被禁止。

示例程序：

    
    	int
    lockfile(int fd) //~ try to lock the file, affecting errno when it fails.
    {
    	struct flock fk;
    	fk.l_type = F_WRLCK;
    	fk.l_start = 0;
    	fk.l_whence = SEEK_SET;
    	fk.l_len = 0;
    	return (fcntl(fd, F_SETLK, &fk));
    }
    
    	int
    already_running(const char* fname) //~ return 1, if another daemon is running
    {
    	int fd;
    	fd = open(fname, O_RDWR|O_CREAT, S_IRUSR|S_IWUSR|S_IRGRP|S_IROTH);
    	if( fd < 0 )
    	{
    		//~ in daemon process, you should use syslog, instead of stdout, for logging
    		//~ syslog(LOG_ERR, "cannot open %s: %s", fname, strerror(errno));
    		fprintf(stdout, "cannot open %s: %s", fname, strerror(errno));
    		fflush(stdout);
    		exit(1);
    	}
    	if( lockfile(fd) < 0 )
    	{
    		if( errno == EACCES || errno == EAGAIN )
    		{Linux 2.6 中的文件锁
    			//~ syslog(LOG_ERR, "cannot lock %s: %s", fname, strerror(errno));
    			fprintf(stdout, "cannot lock %s: %s", fname, strerror(errno));
    			close(fd);
    			return 1;
    		}
    		//~ syslog(LOG_ERR, "cannot lock %s: %s", fname, strerror(errno));
    		fprintf(stdout, "cannot lock %s: %s", fname, strerror(errno));
    		fflush(stdout);
    		exit(1);
    	}
    	ftruncate(fd, 0);
    	char buf[16];
    	sprintf(buf, "%ld", (long)getpid());
    	write(fd, buf, strlen(buf) + 1);
    	return 0;
    }


参考：

《[守护进程的单例运行](http://www.dutor.net/index.php/2010/09/daemon-process-singleton/)》

《[守护进程的单实例实现](http://hi.baidu.com/xioazhu/blog/item/d4e526a451f184fc9152eee9.html)》

《[Linux 2.6 中的文件锁](http://www.ibm.com/developerworks/cn/linux/l-cn-filelock/index.html)》

《[Unix单实例后台守护进程的“规范方法”](http://apt-blog.net/unix_program_daemonize)》
