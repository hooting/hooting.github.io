---
layout: post
title: "OC extension 的使用"
subtitle: ""
category: OC
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [iOS,OC]
---

OC Extension 的主要作用是为类添加“私有”方法，外部不能调用。

Category 和 Extension 的区别：

一、作用：

Category 为没有源代码的类添加方法。

Extension 管理类的私有方法。

二、格式

Category 定义一对.h和.m 文件

Extension 把代码写到原有类的.m文件中

Extension 用法如下：

    #import "Person.h"
    @interface Person ()
    - (void)sayHello;
    @end

    @implementation Person
    - (void)sayHello
    {
        NSLog(@"Hello");
    }
    - (void)say
    {
        NSLog(@"hi");
        [self sayHello];
    }
    @end

在Person.m文件添加私有方法sayHello，内部调用。







