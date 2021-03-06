---
author: admin
comments: true
date: 2012-06-19 11:35:37+00:00
layout: post
slug: how-developers-with-projects
title: 开发人员如何带项目
wordpress_id: 409
categories:
- 开发知识
- 生活感悟
tags:
- pm
- 代码
- 开发
- 心得
---

　　在正规的项目中，会有pm、产品和开发等多种角色，项目的总体节奏基本上都是由pm来掌控的。在《[如何在pm和de之间生存](http://yunpengzhang.com/articles/294.html)》的文章中也有所介绍，作为一名开发人员如何当好pm一职。今天说的不是这个，其实作为一名开发，在项目中也是要起带动作用的，也要有所发挥才能让项目更顺利。

<!-- more -->
　　
　　**如何制定项目实现方案**
　　
　　互联网产品唯快不破，一般在开发前大家就摩拳擦掌，恨不得马上就打开编译器写代码。“走错了方向，走的越快，离目标越远”。在制定方案的时候切记不可轻率，要多想，多评审。做到可扩展和平滑升级，要做到心中有数。支撑多少的请求量，能够满足什么需求，不能满足什么需求。我们的产品在未来发展会不会突破架构。说起来简单，未来有很多是难以预测的，要想一步到位还真的挺难。
　　
　　三个臭皮匠赛过诸葛亮。自己想不通，可以拉上项目组的所有开发，架构师一起评审，一起想。一般自己想好方案，经过推敲后输出文档。发送给评审团，约好时间。在开会前可以先大概写出重点和需要评审的薄弱环节。方便大家帮助思考，也让时间少的同事可以掌握概况。评审的时候要做好主持人，不要让大家发散让会议跑题。
　　
　　会后输出会议纪要，同时修改方案，制定详细方案。与一通开发的同事一起分享，方便大家互相知道开发的思想。一来可以把错误尽早发现解决，二来能够大家互为主备容易接手业务。
　　
　　**如何与产品pk**
　　
　　有了明确的方案之后，对于产品的需求，可以分门别类。哪些能做，哪些不能做，哪些可以一起做，哪些先做，哪些后做……
　　
　　原则是要对产品确实有好处，言之有物，有凭有据。现在做不了的以后可以做，永远做不了的有替代方案可以实现，如果需求真的不靠谱，那么可以pk掉。

　　产品希望功能越来越炫，达到极致。考虑到投入产出比，在时间和成本间要做个折衷，即使现在满足不了，在未来需要也有升级可能。
　　
　　**如何把握开发节奏**
　　
　　项目时间不宜过长，这个更多来说是pm的事情。但是如果多项目并发，还是不要拖得太久。时间久了人就会关注的少，而且一个事情总放在那里，难免会有些情绪反应。这个时候有责任提醒pm发布版本，观察市场效果，如果做好了还拖好久，还不如晚加入，把精力投入到更紧急的项目中去。
　　
　　**如何开发好程序**
　　
　　代码要写的尽量好些，起码要用格式化软件让代码格式整洁。减少耦合，减少耦合，减少耦合！！！代码尽量少耦合，方便需求变更和代码重用，也方便他人熟悉代码。可以想，如果这段代码开源出去，我会不会感到可以。如果不可以，还是改一下，就像一个人的着装，写字。可能邋遢些，字写的丑也没人管你，但是还是好一点让别人和自己舒服些。
　　
　　文档要有记录，代码要有注释。文档信息少，让人摸不着头脑，多呢，也让人难受。如果有歧义会更加浪费时间。所以文档和代码注释要即使更新，而且要保存到svn中，方便大家观看和修改。
　　
　　**如何交接**
　　
　　项目交接要检查代码是否都上传到svn，文档是否健全。把代码中的bug和容易出问题的地方交代清楚，最好都写道文档中。最完美的是有文档只看文档就搞定，但是如果实现不了就多解答下，态度要好要谦虚。也可以问下第三方的体会，认为项目做的如何，好以后改进。
　　
　　总之作为一名开发还是要有自己的责任感，写出健壮的代码和产品，为自己的职业增加成就感。
