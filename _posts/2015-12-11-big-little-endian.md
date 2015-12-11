---
layout:     post
title:      "主机字节序"
subtitle:   ""
date:       2015-11-23 12:00:00
author:     "Hooting"
header-img: "img/post-bg-2015.jpg"
tags:
    - c
    - os
---

## 字节序的种类
考虑一个16位整数，它由两个字节组成。内存中存储这两个字节有两种方法：

* 将低序字节存储在起始地址，这被称为小端（little－endian）字节序
* 将高序字节存储在起始地址，这被称为大端（big－endian）字节序

![](/img/post/little-big-endian.png)

术语“小端”和“大端”表示多个字节值的哪一端（小端或大端）存储在该值的起始位置。
网络协议使用大端字节序来传送多字节整数。


## 测试本机字节序
两种格式都有系统使用，把某个给定系统所用的字节序称为主机字节序。下面的程序输出主机字节序。

```c
#include <stdio.h>

int main(int argc, char **argv)
{
    union {
        short s;
        char c[sizeof(short)];
    } un;
    
    un.s = 0x0102;
    if (sizeof(short) == 2){
        if (un.c[0] == 1 && un.c[1] == 2)
            printf("big-endian\n");
        else if (un.c[0] == 2 && un.c[1] == 1)
            printf("little-endian");
        else
            printf("unknown\n");
        
    } else
        printf("sizeof(short) = %d\n", sizeof(short));
    
}
```