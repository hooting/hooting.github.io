---
layout: post
title: "OC category 类目的使用"
subtitle: ""
category: OC
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [iOS,OC]
---

OC 有个很重要的特性，Category，类目，类目的主要作用是为 没有源代码的类添加方法。

通过Category添加的方法会成为原类的一部分，从而达到扩展一个类的功能。

当你使用 NSString 这个类，想要一个 sayHello 的方法，但是NSString类没有这个方法，就需要为这个类添加方法。

如下操作：

新建文件，选择Objective-C category， 文件名Hello, Category on 选择 NSString。

自动生成 NSString+Hello.h 和 NSString+Hello.m 文件。

在 NSString+Hello.h 文件写上 
    
    @interface NSString (Hello)
    - (void)sayHello;
    @end

在 NSString+Hello.m 文件写上

    @implementation NSString (Hello)
    - (void)sayHello
    {
        NSLog(@"Hello %@", self);
    }
    @end

你自定义的类，做好了。

在main.m中，#import "NSString+Hello.h" 自定义类文件，调用代码如下：

    NSString *str = @"Ellen";
    [str sayHello];

如果输出 Hello Ellen , 那你拓展 NSString 类就成功了。


Category类目 与 Subclass子类 的区别

一、功能
    
Category只能为类添加方法

Subclass既能为类添加方法也能添加变量

二、特点
    
Category新添加的方法会成为原始类的一部分，能被子类继承。

Subclass新添加的方法只有子类才有，父类不具有

三、使用
    
Category，（如果是-方法）使用原有类的实例 或者（如果是+方法）原始类调用方法

Subclass子类才能调用，如果项目开发到尾声的时候，使用子类添加了方法，需要对已写代码做类型替换（将父类替换为子类。）






