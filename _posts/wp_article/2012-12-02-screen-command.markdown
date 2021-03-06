---
author: admin
comments: true
date: 2012-12-02 14:08:36+00:00
layout: post
slug: screen-command
title: screen命令使用
wordpress_id: 590
categories:
- 博客相关
---

screen是一个比较好用的工具，用来管理会话，登录多台主机非常有用。在联调时，一个窗口抓包，另外一个窗口执行程序看log等非常方便，开发的时候多个窗口切换也应对自如。

参考：[linux 技巧：使用 screen 管理你的远程会话](http://www.ibm.com/developerworks/cn/linux/l-cn-screen/)

用man看screen好长，暂时就用用得上的，其他的等需要的时候再查，工具主要还是要多用才行，一部分用熟练了再多学习，否则看了一堆也忘得快。

常用快捷键：

C-a ? 显示所有键绑定信息
C-a w 显示所有窗口列表
C-a C-a 切换到之前显示的窗口
C-a c 创建一个新的运行shell的窗口并切换到该窗口
C-a n 切换到下一个窗口
C-a p 切换到前一个窗口(与C-a n相对)
C-a 0..9 切换到窗口0..9
C-a a 发送 C-a到当前窗口
C-a d 暂时断开screen会话
C-a k 杀掉当前窗口
C-a [ 进入拷贝/回滚模式

-R 　先试图恢复离线的作业。若找不到离线的作业，即建立新的screen作业。

-S <作业名称> 　指定screen作业的名称。

-d -m 启动一个开始就处于断开模式的会话
-r sessionowner/ [pid.tty.host] 重新连接一个断开的会话。多用户模式下连接到其他用户screen会话需要指定sessionowner，需要setuid-root权限
-S sessionname 创建screen会话时为会话指定一个名字
-v 显示screen版本信息
-wipe [match] 同-list，但删掉那些无法连接的会话

.screen配置文件，主要解决了终端配色和乱码问题，看起来还比较顺眼。

    
    #显示256颜色
    attrcolor b ".I"    # 允许加粗色彩--由于某些原因是必须的
    termcapinfo xterm 'Co#256:AB=\E[48;5;%dm:AF=\E[38;5;%dm'   # 告诉screen如何设置颜色。AB 指背景，AF 指前景
    defbce on    # 使用当前背景色来显示删除的字符
    
    encoding UTF-8
    altscreen on
    attrcolor b ".I"
    defbce "on"
    vbell off
    startup_message off
    defscrollback 5000
    bufferfile "$HOME/.screen/exchange"
    hardcopydir "$HOME/.screen/hardcopy"
    sorendition dY
    hardstatus alwayslastline
    sorendition dY
    
    hardstatus off
    hardstatus alwayslastline
    hardstatus string '%{= kG}[ %{G}%H %{g}][%= %{= kw}%?%-Lw%?%{r}(%{W}%n*%f%t%?(%u)%?%{r})%{w}%?%+Lw%?%?%= %{g}][%{B} %m-%d %{W} %c %{g}]'
