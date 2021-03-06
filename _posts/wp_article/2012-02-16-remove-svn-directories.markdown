---
author: admin
comments: true
date: 2012-02-16 09:36:48+00:00
layout: post
slug: remove-svn-directories
title: 删除svn目录
wordpress_id: 309
categories:
- 脚本操作
tags:
- find 删除 svn
---

当向svn添加目录时，如果目录下有.svn目录，会出现冲突。

所以在引用代码时总是先删除掉.svn目录，使用以下命令。

[code lang="cpp"]
find . -name ".svn" -exec rm -rf {} \;

find . -name ".svn" | xargs rm -rf
[/code]

具体的含义可以参考[《find命令总结》](http://yunpengzhang.com/articles/53.html)

find命令将所有匹配到的文件一起传递给exec执行,有些系统对能够传递给exec的命令长度有限制，这样在find命令运行几分钟之后，就会出现溢出错误。

注意：在删除文件前最好先打印一下查找到的文件，核对下是否是要删除的。
