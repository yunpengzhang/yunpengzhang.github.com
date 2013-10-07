---
author: admin
comments: true
date: 2011-10-10 15:57:03+00:00
layout: post
slug: car-sheep-problem
title: 车羊问题
wordpress_id: 123
categories:
- 有趣问题
tags:
- 车羊问题，蒙提霍尔，智力，概率
---

问题如下：


> 　　
　　一个游戏：有3扇关闭着的门，其中2扇门后面各有一只羊，另一扇门后面有一辆车。
　　
　　参与者：一个游戏者和一个主持人。主持人事先知道各扇门后的物品，而游戏者不知道。
　　
　　游戏目的：游戏者选择到车。
　　
　　游戏过程：1、游戏者随机选定一扇门；2、在不打开此扇门的情况下，主持人打开另一扇有羊的门。3、此时面对剩下2扇门，游戏者有一次更改上次选择的机会。
　　
　　问题是：游戏者是否应该改变上次的选择，以使选到车的概率较大？




记得之前同事给我出过这个问题，当时大家的答案也是换和不换两种，有的认为是换概率是2/3,有的认为是不换和换都一样，概率是50%，因为后面剩两个概率是相同的。
　　
　　我认为应该换，因为开始选中羊的概率是2/3,这时换了就能够得到车，所以换能得到车的概率是2/3。
　　
　　从前在网上看到过这个问题，当时在美国智商最高的人回答也是2/3，还引起了很大的争议。
　　
　　在网上搜到了，问题的学名叫《蒙提霍尔问题》，[这里](http://baike.baidu.com/view/657835.htm#4)有来龙去脉。
　　
　　我也写了个小程序来模拟这个问题，代码和结果如下，也是2/3的概率选中车，如果在主持人提示后换的话。
　　
　　这样的小问题为什么会有这么多人不同意见呢？是我们的模型不对吗，还是概率没学好？如果有哪位朋友能给出数学公式证明，请联系我，非常感兴趣。
　　
　　提示：应该和贝叶斯公式有关，是在条件下的概率问题。

[code lang="cpp"]
#include "stdio.h"
#include "stdlib.h"
#include "string.h"
#include "time.h"

#define DOOR_COUNT 3
int getrand( int max )
{
    double max_rand = RAND_MAX * 1.0;
    return (rand() /max_rand) * max;
}
int choose( int changed )
{
    char door[ DOOR_COUNT ] = { 0 };
    int moto_index = getrand( DOOR_COUNT );
    door[ moto_index ] = 1;
    int choose_index = getrand( DOOR_COUNT );
    int left_index = -1;
    int display_index = -1;
    int i;
    for (i = 0; i < DOOR_COUNT; i++) {
        if( i == choose_index  )
            continue;
        if( i == moto_index )
            continue;
        display_index = i;
        break;
    }
    for( i=0; i < DOOR_COUNT; ++i )
    {
        if( i == choose_index  )
            continue;
        if( i == display_index )
            continue;
        left_index = i;
    }
    printf("moto_index = %d first_choose_index = %d display_index = %d left_index = %d\n",
            moto_index, choose_index, display_index, left_index);
    if( changed == 1)
        choose_index = left_index;
    if( choose_index == moto_index )
        return 1;
    return 0;
}
int main(int argc, const char *argv[])
{
    srand( ( unsigned  )time( NULL ) );
    int count = 10000;
    int changed = 1;
    int motocount = 0;
    int i;
    for (i = 0; i < count; i++) {
        int iRet = choose( changed );
        motocount += iRet;
    }
    printf( "changed = %d count = %d motocount = %d, rate = %0.2f\n",
            changed, count, motocount, motocount*1.0/count);
    system( "pause" );
    return 0;
}
[/code]

![运行结果](http://img165.poco.cn/mypoco/myphoto/20111010/23/6449994220111010234306094.jpg)
