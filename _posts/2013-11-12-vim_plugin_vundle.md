---
layout: post
title: "vim插件之vundle"
description: ""
category: "vim"
tags: [vim, script, vundle]
---
{% include JB/setup %}

##前言
[上一节][1]把vim配置得可以使用了，但是都是从别人那抄过来的，有些东西是什么并不知道,作为一个vim的“老“用户，
知其然还要知其所以然。从这节开始，将逐步学习遇到的插件，今天就学习管理插件的插件“vundle”。

##什么是vundle？
回顾最初使用vim的时候，从网上搜到插件的名字，然后下载安装插件到.vim目录。如果好用简直欣喜若狂，为了把插件保存
起来，经常会压缩备份，用日期当名字，然后存到邮箱或u盘里。

但是这样很不方便，依赖的模块太多，没有一个配置文件就能把配置都配好的清爽。一般折腾久了的人都是这个想法。

vundle是管理vim插件的一个插件。是模仿ruby语言的bundle来实现的。

有了vundle，您只需要把插件的名字写到vimrc文件中就可以了，运行vundle的命令，就自动联网下载安装。
如果删除，更新软件，也不用到vim文件夹中查找删除，直接运行命令就可以了。甚至还可以用插件名字查找插件，选择安装。

每次重装linux系统时也希望有这样的工具，把所有需要都软件都下载装好。

##如何安装，使用vundle？
1.安装git软件。
2.执行命令安装vundle。
    `git clone https://github.com/gmarik/vundle.git ~/.vim/bundle/vundle`
3.配置vimrc文件，记录要安装的插件名字

---

	set nocompatible               " be iMproved 确保是vim而不是vi
	filetype off                   " required!   必需
	
	set rtp+=~/.vim/vundle.git/   "保留
	call vundle#rc()              "保留
	
	" let Vundle manage Vundle
	Bundle 'gmarik/vundle'        "vundle安装vundle插件
	
	" My Bundles here:             下面都是插件的配置，格式是 Bundle ‘插件名称’
	"
	" original repos on github
	Bundle 'tpope/vim-fugitive'
	Bundle 'Lokaltog/vim-easymotion'
	Bundle 'rstacruz/sparkup', {'rtp': 'vim/'}
	" vim-scripts repos
	Bundle 'L9'
	Bundle 'FuzzyFinder'
	Bundle 'rails.vim'
	" non github repos
	Bundle 'git://git.wincent.com/command-t.git'
	" git repos on your local machine (ie. when working on your own plugin)
	Bundle 'file:///Users/gmarik/path/to/plugin'
	" ...
	
	filetype plugin indent on     " required!
	" or 
	" filetype plugin on          " to not use the indentation settings set by plugins

---


4.在vim的normal状态下输入命令

    `:BundleInstall`
从github上下载配置的插件。由于网络原因，下载的速度可能比较慢，第一次要耐心等待。而且下载完成后会有提示，可以从log中查看出来。

更新插件命令

    `:BundleInstall!`

删除插件

    `:BundleClean`
    
查找插件

   `:BundleSearch foo`

更多使用vundle都方法可以参考·h vundle·来查看，不过主要是管理插件，平时用的几率不高，上面的这些就够用了。

##vundle的原理

vim的脚本都存储在<http://vim-scripts.org/>这个网站，从前大家都是到这里下载安装的。
最近几年github的兴盛，使得用git来管理插件的版本更加方便。同时通过git来下载插件也成为理所应当的事。
在github上，把所有script上的插件都上传到了<https://github.com/vim-scripts>项目中,通过git clone来下载，然后
再配合脚本配置，就把插件管理做的很简单了。

由于vundle的脚本能够很好的描述插件安装这个过程，把一个复杂都问题抽象成为下载，配置两个简单的步骤，使得vundle被越来越多的vimer所使用。

计算机是机器，只要能用最简洁的方式描述清楚对机器的需求，那么计算机的操作对用户就是友好的。虽然把简洁的语言翻译成执行过程会比较复杂，
但是和用户使用简便比起来，都不是问题，而且越能简单描述的东西，越容易理解和维护。

所以vundle不仅仅是个vim插件，从中也可以领悟一些软件设计，程序设计的方法，甚至服务器程序的发布也可以采用这种方法，配置好发布脚本从中央服务器拉取。

[1]:http://www.yunpengzhang.com/blog/2013/11/10/vim_config_tips/

