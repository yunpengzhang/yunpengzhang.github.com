---
author: admin
comments: true
date: 2013-06-14 07:11:25+00:00
layout: post
slug: two-kinds-of-methods-to-modify-the-host-name
title: 两种修改主机名方法
wordpress_id: 686
categories:
- 开发运营
---

一、修改redhat类机器


> cat /etc/sysconfig/network
NETWORKING=yes

HOSTNAME=myservice


二、修改suse机器


> /etc/HOSTNAME
