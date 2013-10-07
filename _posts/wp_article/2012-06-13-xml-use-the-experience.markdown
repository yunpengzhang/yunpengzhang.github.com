---
author: admin
comments: true
date: 2012-06-13 11:53:59+00:00
layout: post
slug: xml-use-the-experience
title: XML使用心得
wordpress_id: 391
categories:
- 开发知识
tags:
- tinyxml
- xml
- xml解析
- 配置
---

最近的一个项目中使用到了xml文件，主要用来读取配置。对xml有了一些了解。为什么要用xml呢？数据的格式方便阅读，擅长做为有层级关系的配置文件。例如：一个系统有3个ip和端口。如果用传统的配置，则要写成ip1 xxx ip2 yyy ip3 zzz。如果加ip呢，如果每个ip都再添加一些属性呢？要用下标来标记，看起来不直观，写起来也麻烦。如果换成xml就会简单许多。

**什么是XML？**

XML 代表Extensible Markup Language（eXtensible Markup Language的缩写，意为可扩展的标记语言）。XML是一套定义语义标记的规则，这些标记将文档分成许多部件并对这些部件加以标识。它也是元标记语言，即定义了用于定义其他与特定领域有关的、语义的、结构化的标记语言的句法语言。

XML 指可扩展标记语言（EXtensible Markup Language）<!-- more -->

XML 是一种标记语言，很类似 HTML

XML 的设计宗旨是传输数据，而非显示数据

XML 标签没有被预定义。您需要自行定义标签

XML 被设计为具有自我描述性。

XML 是 W3C 的推荐标准

XML 文档形成一种树结构

XML 文档必须包含根元素。该元素是所有其他元素的父元素。

XML 文档中的元素形成了一棵文档树。这棵树从根部开始，并扩展到树的最底端。

所有元素均可拥有子元素：

    
    <root>
    　　<child>
    　　　　<subchild>.....</subchild>
    　　</child>
    </root>


**XML到底有什么用途和好处呢？**

XML方便数据传递，不同的程序不同的操作系统，都可以通过XML来传递数据。提供了一种解析的标准和协议，像TLV格式的加强版，协议解析只需解析需要的部分，对于程序扩展向下兼容有好处。

具体详细的XML介绍还请参考下面两篇文章。

[XML 新手入门](http://www.ibm.com/developerworks/cn/xml/newto/) [ XML 教程](http://www.w3school.com.cn/xml/index.asp)

**XML解析程序**

用到了tinyxml，是一个轻量级的xml C++库，对于简单的xml解析还是很方便的，下面简要介绍一下tinyxml的使用和原理。

tinyxml中也是把xml文档定义为TiXmlDocument。一个TiXmlDocument运行了LoadFile方法读取xml文件成功后就把xml文件解析成为一颗树。

每个树的节点用TiXmlNode来表示。TiXmlNode有类型，分为TiXmlNode::TINYXML_DOCUMENT，TiXmlNode::TINYXML_DOCUMENT，TiXmlNode::TINYXML_UNKNOWN，TiXmlNode::TINYXML_DECLARATION，TiXmlNode::TINYXML_TEXT，TiXmlNode::TINYXML_TEXT。由TiXmlNode的Type()方法展示。

TiXmlNode使用ToXXX转换成不同的类型。例如ToElement()转换成元素类型TiXmlElement。遍历element的属性可以是用pElement->FirstAttribute()和pAttrib->Next();

如果要遍历element，要使用TiXmlNode的FirstChild()和NextSibling()来遍历每个element的儿子节点。

tinyxml tutorial 中的 Full listing for dump_to_stdout可以很好的理解xml的遍历。简单的应用也可以修改这个程序搞定。

也可以参考[TinyXml使用与剖析](http://www.wuzesheng.com/?p=260)的介绍理解tinyxml的实现原理。

这里是[tinyxml](http://www.grinninglizard.com/tinyxml/)的主页

TiXmlHandle

    
    TiXmlElement* root = document.FirstChildElement( "Document" );
    if ( root )
    {
        TiXmlElement* element = root->FirstChildElement( "Element" );
        if ( element )
        {
            TiXmlElement* child = element->FirstChildElement( "Child" );
            if ( child )
            {
                TiXmlElement* child2 = child->NextSiblingElement( "Child" );
                if ( child2 )
                {
                    // Finally do something useful.


使用TiXmlHandle类

    
    TiXmlHandle docHandle( &document );
    TiXmlElement* child2 = docHandle.FirstChild( "Document" ).FirstChild( "Element" ).Child( "Child", 1 ).ToElement();
    if ( child2 )
    {
        // do something useful


看过这个例子后，在提取xml文件元素的时候会方便很多，不用每次都遍历在递归中判断。

后续：把从前的代码用handle实现；读tinyxml的源代码。

简单的使用了一下，发现确实使用handle很方便，配合数字可以很容易遍历所有的元素。

对于属性还是要搜索遍历，不过应该也可以使用handle来处理属性。

判断返回的指针是否为空很重要。
