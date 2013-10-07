---
author: admin
comments: true
date: 2011-10-10 04:47:34+00:00
layout: post
slug: windows7-locally-installed-mediawiki-and-wordpress
title: windows7 本地安装mediawiki和wordpress
wordpress_id: 50
categories:
- 博客相关
tags:
- wordpress，wiki，博客
---

　　**1.下载wamp**
　　
　　wamp5 的下载地址为[http://wampserver.com/en/download.php](http://wampserver.com/en/download.php)
　　
　　安装只要默认下一步就可以，用户名是root，数据库密码空。
　　
　　**2.下载mediawiki**
　　
　　mediawiki 的下载地址为：[http://prdownloads.sourceforge.net/wikipedia/mediawiki-1.8.2.tar.gz?download](http://prdownloads.sourceforge.net/wikipedia/mediawiki-1.8.2.tar.gz?download)
　　
　　将mediawiki程序解压，将解压后主文件夹下的所有文件都copy到wamp5安装目录下的www子目录，通过浏览器访问index.php文件（例如[http://localhost/config/index.php](http://localhost/config/index.php) )，这时安装过程自动开始，软件会检查系统配置。之后，根据页面提示填写相应的内容：（请对以下内容做好记录，以便升级时使用）具体可参考windows 下的wiki安装
　　
　　**3.下载wordpress**
　　
　　参考《Windows WordPress本地安装教程》
　　
　　不同的地方是放到wamp，按照提示访问
　　
　　[http://localhost/wordpress/wp-admin/install.php](http://localhost/wordpress/wp-admin/install.php)也可参考《WordPress详细安装步骤》
　　
　　还有《[cPanel安装WordPress中文教程](http://hugege.com/2009/03/16/cpanel-wordpress-china/)》也很好，就是参考这篇才把博客搭建好的。
