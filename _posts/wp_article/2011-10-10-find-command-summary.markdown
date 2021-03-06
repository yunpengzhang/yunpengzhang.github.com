---
author: admin
comments: true
date: 2011-10-10 04:49:46+00:00
layout: post
slug: find-command-summary
title: find命令总结
wordpress_id: 53
categories:
- shell
tags:
- shell，find
---

　　中午有个同事要使用find来查找删除文件，之前也查过不少次，这次把常用的功能记录下来，少做重复功。具体详细的可以参考man和《Linux文件查找命令find,xargs详述》。
　　
　　一、find命令的格式
　　
　　1.find命令一般格式
　　
　　find pathname -options [-print -exec -ok ...]
　　
　　2.exec和ok命令区别
　　
　　-exec： find命令对匹配的文件执行该参数所给出的shell命令。相应命令的形式为'command' {} \;，注意{}和\；之间的空格。
　　
　　-ok： 和-exec的作用相同，只不过以一种更为安全的模式来执行该参数所给出的shell命令，在执行每一个命令之前，都会给出提示，让用户来确定是否执行。
　　
　　exec选项后面跟随着所要执行的命令或脚本，然后是一对儿{}，一个空格和一个\，最后是一个分号。
　　
　　二、应用场景
　　
　　1.在/logs目录中查找更改时间在5日以前的文件并删除它们：
　　
　　$ find logs -type f -mtime +5 -exec rm {} \;
　　
　　注意：删除一定要小心，要先用ls或-print查看是否是要删的文件。
　　
　　2.find文件后在文件的集合中查找文件内容：
　　
　　# find /etc -name "passwd*" -exec grep "sam" {} \;
　　
　　sam:x:501:501::/usr/sam:/bin/bash
　　
　　3.让当前目录中文件属主具有读、写权限，并且文件所属组的用户和其他用户具有读权限的文件；
　　
　　$ find . -type f -perm 644 -exec ls -l {} \;
　　
　　-perm mode:文件许可正好符合mode
　　
　　-perm +mode:文件许可部分符合mode
　　
　　-perm -mode: 文件许可完全符合mode
　　
　　4、find命令将删除当目录中访问时间在7日以来、含有数字后缀的admin.log文件。
　　
　　该命令只检查三位数字，所以相应文件的后缀不要超过999。先建几个admin.log*的文件 ，才能使用下面这个命令
　　
　　$ find . -name "admin.log[0-9][0-9][0-9]" -atime -7  -ok rm {} \;
　　
　　< rm ... ./admin.log001 > ? n
　　
　　< rm ... ./admin.log002 > ? n
　　
　　< rm ... ./admin.log042 > ? n
　　
　　< rm ... ./admin.log942 > ? n
　　
　　5.查找有执行权限的文件（读写可能有也可能没有）
　　
　　$find . -perm +001
　　
　　6.查找所有大于1M的文件
　　
　　$find . -size +10000000c
　　
　　7.在当前目录查找，不进入子文件夹
　　
　　find . -maxdepth 1 -name “*.c”
　　
　　8.使用正则表达式
　　
　　# find . -regex ‘[a-z]+\.c′
　　
　　9.在当前目录查找，但不进入某些目录下
　　
　　$ find /apps -path "/apps/bin" -prune -o -print
　　
　　find /usr/sam -path "/usr/sam/dir1" -prune -o -print
　　
　　find [-path ..] [expression] 在路径列表的后面的是表达式
　　
　　find /usr/sam \( -path /usr/sam/dir1 -o -path /usr/sam/file1 \) -prune -o -print
　　
　　三、xargs
　　
　　xargs - build and execute command lines from standard input
　　
　　在使用find命令的-exec选项处理匹配到的文件时， find命令将所有匹配到的文件一起传递给exec执行。但有些系统对能够传递给exec的命令长度有限制，这样在find命令运行几分钟之后，就会出现溢出错误。错误信息通常是“参数列太长”或“参数列溢出”。这就是xargs命令的用处所在，特别是与find命令一起使用。
　　
　　而使用xargs命令则只有一个进程。另外，在使用xargs命令时，究竟是一次获取所有的参数，还是分批取得参数，以及每一次获取参数的数目都会根据该命令的选项及系统内核中相应的可调参数来确定。
　　
　　xargs使用例子
　　
　　#find . -name "file*" -print | xargs echo "" > /temp/core.log
　　
　　# cat /temp/core.log
　　
　　./file6
　　
　　用grep命令在所有的普通文件中搜索hostname这个词
　　
　　# find . -type f -print | xargs grep "hostname"
　　
　　./httpd1.conf:#     different IP addresses or hostnames and have them handled by the
　　
　　./httpd1.conf:# VirtualHost: If you want to maintain multiple domains/hostnameson your
　　
　　$ ls | xargs-t -i mv {} {}.bak
　　
　　-i 选项告诉xargs 用每项的名称替换 {}。
　　
　　-t是在执行之前把错误信息打印到标准输出。
