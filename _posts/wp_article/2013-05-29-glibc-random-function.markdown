---
author: admin
comments: true
date: 2013-05-29 06:11:33+00:00
layout: post
slug: glibc-random-function
title: glibc随机函数
wordpress_id: 649
categories:
- 开发知识
tags:
- glibc
- rand
- random
- srand
- 伪随机
- 随机
- 随机函数
---

一、如何使用随机函数
查看man可以找到
#include <stdlib.h>

int rand(void);

int rand_r(unsigned int *seedp);

void srand(unsigned int seed);

rand返回0~RAND_MAX之间的整数，是“伪随机”
srand()用来设置rand函数的随机种子，随机种子相同产生的随机数序列也是相同的。
如果没调用srand那么rand函数自动使用1作为种子。

在函数初始化的时候srand()调用设置一次随机种子就可以里，不必每次调用rand都使用srand调用。

随机函数原理
RAND_MAX的值为32767
原理是使用Linear-Congruential-Random-Number-Generator算法产生伪随机数。（具体的我还没弄明白）弄明白再补一篇。
函数如下



    
    static int32_t randtbl[DEG_3 + 1] =
      {
        TYPE_3,
    
        -1726662223, 379960547, 1735697613, 1040273694, 1313901226,
        1627687941, -179304937, -2073333483, 1780058412, -1989503057,
        -615974602, 344556628, 939512070, -1249116260, 1507946756,
        -812545463, 154635395, 1388815473, -1926676823, 525320961,
        -1009028674, 968117788, -123449607, 1284210865, 435012392,
        -2017506339, -911064859, -370259173, 1132637927, 1398500161,
        -205601318,
      };
    /* Initialize the random number generator based on the given seed.  If the
       type is the trivial no-state-information type, just remember the seed.
       Otherwise, initializes state[] based on the given "seed" via a linear
       congruential generator.  Then, the pointers are set to known locations
       that are exactly rand_sep places apart.  Lastly, it cycles the state
       information a given number of times to get rid of any initial dependencies
       introduced by the L.C.R.N.G.  Note that the initialization of randtbl[]
       for default usage relies on values produced by this routine.  */
    int
    __srandom_r (seed, buf)
         unsigned int seed;
         struct random_data *buf;
    {
      int type;
      int32_t *state;
      long int i;
      int32_t word;
      int32_t *dst;
      int kc;
    
      if (buf == NULL)
        goto fail;
      type = buf->rand_type;
      if ((unsigned int) type >= MAX_TYPES)
        goto fail;
    
      state = buf->state;
      /* We must make sure the seed is not 0.  Take arbitrarily 1 in this case.  */
      if (seed == 0)
        seed = 1;
      state[0] = seed;
      if (type == TYPE_0)
        goto done;
    
      dst = state;
      word = seed;
      kc = buf->rand_deg;
       for (i = 1; i < kc; ++i)
         {
           /* This does:
            state[i] = (16807 * state[i - 1]) % 2147483647;
          but avoids overflowing 31 bits.  */
           long int hi = word / 127773;
           long int lo = word % 127773;
           word = 16807 * lo - 2836 * hi;
           if (word < 0)
         word += 2147483647;
           *++dst = word;
         }
    
       buf->fptr = &state[buf->rand_sep];
       buf->rptr = &state[0];
       kc *= 10;
       while (--kc >= 0)
         {
           int32_t discard;
           (void) __random_r (buf, &discard);
         }
    
      done:
       return 0;
    
      fail:
       return -1;
     }



    
    int
    __random_r (buf, result)
         struct random_data *buf;
         int32_t *result;
    {
      int32_t *state;
    
      if (buf == NULL || result == NULL)
        goto fail;
    
      state = buf->state;
    
      if (buf->rand_type == TYPE_0)
        {
          int32_t val = state[0];
          val = ((state[0] * 1103515245) + 12345) & 0x7fffffff;
          state[0] = val;
          *result = val;
        }
      else
        {
          int32_t *fptr = buf->fptr;
          int32_t *rptr = buf->rptr;
          int32_t *end_ptr = buf->end_ptr;
          int32_t val;
    
          val = *fptr += *rptr;
          /* Chucking least random bit.  */
          *result = (val >> 1) & 0x7fffffff;
          ++fptr;
          if (fptr >= end_ptr)
        {
          fptr = state;
          ++rptr;                                              
        }
          else
        {
          ++rptr;
          if (rptr >= end_ptr)
            rptr = state;
        }
          buf->fptr = fptr;
          buf->rptr = rptr;
        }
      return 0;
    
     fail:                  
      __set_errno (EINVAL);
      return -1;
    }




可以看到，设置随机种子的时候操作很复杂，如果每次rand都调用srand是非常耗时，而且没有用途的。特别是有人总是用srand(time(NULL))，那样一秒钟产生的随机数都是相同的，根本没有作用。



二、使用场景

如果只是为了使用随机得到尽量乱平均的数字，可以使用rand函数，或者使用自己写的

    
    static unsigned long next = 1;
    
    /* RAND_MAX assumed to be 32767 */
    int myrand(void) {
    next = next * 1103515245 + 12345;
    return((unsigned)(next/65536) % 32768);
    }
    
    void mysrand(unsigned seed) {
    next = seed;
    }




也能达到效果，例如在寻址函数中随机挑选一个服务器，为的就是平均，即使轮询影响也不大。

如果是为了加密，构造随机数，那么rand函数不适合你，还是用硬件的函数吧
参考硬件相关的安全随机数：[/dev/random和/dev/urandom](http://yunpengzhang.com/articles/202.html)

参考：
http://blog.csdn.net/flyoxs/article/details/5331718

http://hi.baidu.com/tlogin/item/e619169c2c0dfdc9b6253149

http://hi.baidu.com/g0t3n/item/c3508beaa9a903b22f140baf

http://en.wikipedia.org/wiki/Linear_congruential_generator


