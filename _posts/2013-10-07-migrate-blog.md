---
layout: post
title: "迁移博客"
description: ""
category: "blog"
tags:
- blog
- jekyll
- markdown
- git
---
{% include JB/setup %}

最近把这些年的东西都收拾了一下，发现博客也开了两年了，写的文章数量不是很多，而且最近也没有搭理了。领悟到重要的东西还是要总结起来，归纳到一个地方比较好，其实总结的文字有一张软盘就能存储了，但是备份还很大。

一是很多东西没有抽象成自己的，还在原始文件中；二是使用的文档都有附带的东西，没有把内容和形式分离，耦合很多。

无意间发现markdown，觉得这就是我像要的，使用简单的文本，加些简单的规则，既可以轻松使用文本编辑器查看内容，也可以通过程序转换成美观的格式。

通过对我自己的文章分析，特点如下

1. 文字占比比较多，插图少
2. 使用的格式较少——粗体、斜体、标题基本就够了
3. 主要是自己看

综上，我只需要一个静态的网页就可以了，能够专心写字就最好了，之前买的空间和wordpress真是浪费啊。还有很多炫的博客效果，使用简直是浪费，读者不再乎，我也不在乎。研究了一下jekyll和markdown就迁移过来了。感觉还是不错的。

###迁移参考
迁移jekyll可以参考两个网站[jekyll主页](http://jekyllrb.com/)[jekyllbootstrap主页](http://jekyllbootstrap.com/)

markdown使用教程：<http://wowubuntu.com/markdown/basic.html>

github 介绍<http://www.ruanyifeng.com/blog/2012/07/git.html>

<http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html>

感觉新的博客也没有什么，简单最好，以后主要关注的是内容。迁移完文章只是简单地把主题字体和字号修改下，高亮插件这类都没有使用。还是用原生的最好，经过转化的反而会变得不通用，以来多环境也难搭建。

补充个从前博客的截图，纪念下。
[博客图片](/assets/images/2013-10-07-migrate-blog/wp_blog.jpg)

###对比思考
实现jekyll的技术有jekyll本身，jekyllbootstrap，octopress。jekyll相对来说简单，提供了一种方法，是基础。jekyllbootstrap是一个前端框架，相对来说更容易扩展，当然
也要按照相应模板的规则来部署实现。octopress是一个定制更高，功能更强的框架。包括wordpress，他们都可以做出同样好看的网页，只是难易程度不同。没有
谁好谁不好的区别，主要看那种更适合你。

markdown也是一样，用word，html书写也没什么不好，就看你喜欢那种。我是觉得文本存储少，我所使用的99%都可以用markdown来实现，相对来说很轻量。

总之，没有银弹，把一种技术用对了地方，就是好技术，不必立一个规则，让大家都认为那样最好。首要的是明确需求，缺什么，然后实现就够了。
