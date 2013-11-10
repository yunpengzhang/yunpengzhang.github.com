---
layout: post
title: "vim的IDE配置和vim tips"
description: ""
category: "软件配置"
tags: 
- vim
- IDE
- tips
- 配置
---
{% include JB/setup %}

##写在前面
用vim时间也很久了，但是发现最初从网上cp了一份vimrc和插件后就没怎么大修改过，而且每次备份都是一堆的插件，删除都不知道怎么删。
最近知道了有vundle这个插件，重新配置了一下vim，发现vim有了足够都vimrc和插件后，功能大幅增长。自己平时也爱折腾，立下此文，以后都修改都列到这里。

##VIM 配置
把从前的配置移植过来，用vundle配置了vimrc文件，见最后都附。

##VIM tips
### vim补全时发现候选菜单的上下选择ctrl-p, ctrl-n相反

原因:使用`verbose imap <c-n>` 查看是由于什么设置所致，发现是supertab插件导致，删除解决。


### 附：vimrc文件
<script src="https://gist.github.com/yunpengzhang/33ea66a075bc853fe2ec.js"></script>
