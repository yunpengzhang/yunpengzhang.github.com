---
layout: post
title: "用sed提取url"
description: ""
category: ""
tags: [sed,正则]
---
{% include JB/setup %}

使用sed提取文本里的url，文本格式如下，都是

    百度 http://www.baidu.com
    sohu http://www.sohu.com

提取语句如下

    sed 's/\(.*\)\(http.*\)/\2/g' url.txt

第一个`\(.*\)`表示匹配任何字符串
第二个`\(http.*\）`表示匹配http开头的字符串，这样就把一行分成了两个串，最后
用`\2`来代替整个串。
再sed用`\`来进行转义。
