---
author: admin
comments: true
date: 2012-06-01 05:41:37+00:00
layout: post
slug: mysql-database-to-achieve-two-master-slave-synchronization-between
title: 实现两个Mysql数据库之间的主从同步
wordpress_id: 363
categories:
- 数据库
---

主要参考《实现两个Mysql数据库之间的主从同步》
[http://blog.csdn.net/guoguo1980/article/details/2343722](http://blog.csdn.net/guoguo1980/article/details/2343722)的文章。

注意：两个mysql的**版本要相同**，否则会有问题。


###### **一、在master机器。**


1.在master机器上建立帐户并赋予权限。

    
    grant SUPER,REPLICATION CLIENT on *.* to 'backup'@'xxx.xxx.xxx.xxx' identified by '1234';
    grant replication slave on *.* to 'backup'@'xxx.xxx.xxx.xxx' identified by '1234';
    grant file on *.* to 'backup'@'xxx.xxx.xxx.xxx' identified by '1234';


2、增加一个数据库作为同步数据库：

    
    create database test;


<!-- more -->
3、创建一个表结构：

    
    create table mytest (username varchar(20),password varchar(20));


4、修改配置文件：

修改A的/etc/my.cnf文件，在my.cnf配置项中加入下面配置：

    
    server-id = 1      #Server标识
    log-bin
    binlog-do-db=test  #指定需要日志的数据库


5、重起数据库服务：

    
    service mysqld restart


查看server-id：

    
    show variable like 'server_id';


实例：

mysql> show variables like 'server_id';

+---------------+-------+

| Variable_name | Value |

+---------------+-------+

| server_id     | 1     |

+---------------+-------+

1 row in set (0.00 sec)

6、用show master status\G命令看日志情况。

正常为：

mysql> show master status\G

*************************** 1. row ***************************

File: mysqld-bin.000002

Position: 198

Binlog_Do_DB: test,test

Binlog_Ignore_DB:

1 row in set (0.08 sec)


### **二、在slave机器。**


1、增加一个数据库作为同步数据库：

    
    create database test;


2、创建一个表结构：

    
    create table mytest (username varchar(20),password varchar(20));


3、修改配置文件：

修改B的/etc/my.cnf文件，在my.cnf配置项中加入下面配置：

server-id=2

master-host=10.10. 0.119

master-user=backup    #同步用户帐号

master-password=1234

master-port=3306

master-connect-retry=60 #预设重试间隔60秒

replicate-do-db=test    #告诉slave只做backup数据库的更新

5、重起数据库服务：

service mysqld restart

查看server-id：

show variables like 'server_id'；

实例：

mysql> show variables like 'server_id';

+---------------+-------+

| Variable_name | Value |

+---------------+-------+

| server_id     | 2     |

+---------------+-------+

1 row in set (0.00 sec)

6、用show slave status\G命令看日志情况。

正常为：

mysql> show slave status\G

*************************** 1. row ***************************

Slave_IO_State: Waiting for master to send event

Master_Host: 10.10.0.119

Master_User: backup

Master_Port: 3306

Connect_Retry: 60

Master_Log_File: mysqld-bin.000001

Read_Master_Log_Pos: 98

Relay_Log_File: mysqld-relay-bin.000003

Relay_Log_Pos: 236

Relay_Master_Log_File: mysqld-bin.000001

Slave_IO_Running: Yes

Slave_SQL_Running: Yes

Replicate_Do_DB: test,test

Replicate_Ignore_DB:

Replicate_Do_Table:

Replicate_Ignore_Table:

Replicate_Wild_Do_Table:

Replicate_Wild_Ignore_Table:

Last_Errno: 0

Last_Error:

Skip_Counter: 0

Exec_Master_Log_Pos: 98

Relay_Log_Space: 236

Until_Condition: None

Until_Log_File:

Until_Log_Pos: 0

Master_SSL_Allowed: No

Master_SSL_CA_File:

Master_SSL_CA_Path:

Master_SSL_Cert:

Master_SSL_Cipher:

Master_SSL_Key:

Seconds_Behind_Master: 0

1 row in set (0.01 sec)


### **三、验证配置**


分别使用insert, delete , update在A主机进行增删改查数据库；查看B主机的数据库是否与A主机一致；若一致，则配置成功。


### **四、双机互备模式**


如果在A主机加入slave设置，在B主机加入master设置，则可以做B->A的同步。

1、在A主机的配置文件中 mysqld配置项加入以下设置：

master-host=10.10.8.112

master-user=backup

master-password=1234

replicate-do-db=test

master-connect-retry=10

2、在B的配置文件中 mysqld配置项加入以下设置：

log-bin

binlog-do-db=test

注意：当有错误产生时，*.err日志文件同步的线程退出，当纠正错误后，要让同步机制进行工作，运行slave start。

重起A、B机器，则可以实现双向的热备份。


### **五、常见问题及解决**


1、Slave机器的权限问题，不但要给slave机器File权限，还要给它REPLICATION SLAVE的权限。

2、在修改完Slave机器/etc/my.cnf之后，slave机器的mysql服务启动之前，记得要删除掉master.info

3、在show master status或着show slave status不正常时，看看.err是怎样说的。

4、Slave上Mysql的Replication工作有两个线程, I/O thread和SQL thread。I/O的作用是从master 3306端口上把它的binlog取过来(master在被修改了任何内容之后,就会把修改了什么写到自己的binlog等待slave更新),然后写到本地的relay-log,而SQL thread则是去读本地的relay-log,再把它转换成本Mysql所能理解的语句，于是同步就这样一步一步的完成.决定I/O thread的是/var/lib/mysql/master.info,而决定SQL thread的是/var/lib/mysql/relay-log.info.

5、启动slave，命令用start slave；重新启动用restart slave

重启可以使用

service mysqld restart

或者

mysqladmin -u root -p shutdown

/usr/local/mysql/bin/safe_mysqld  --user=mysql &

回收命令可以使用

revoke file on *.* from 'user'@'xxx.xxx.xxx.xxx';

删除用户

drop user 'user'@'xxx.xxx.xxx.xxx';

load data from master
