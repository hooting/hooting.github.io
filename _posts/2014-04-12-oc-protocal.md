---
layout: post
title: "OC protocal 的使用"
subtitle: ""
category: OC
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [iOS,OC]
---

Protocal又名协议，协议是一套标准（一堆方法的声明），只有.h文件。

接受协议的对象，实现协议中定义的方法。

新建文件，选择Objective-C protocal，名字为 MarrayProtocol

Protocal 以 @protocal，以@end结束，例如：

    @protocol MarrayProtocol <NSObject>
    @required
    - (void)makeMoney;
    @optional
    - (void)washcloth;
    @end


@required后的方法是必须实现的，@required可写可不写。

@optional后方法是可选实现的，可实现，可不实现。

协议定义好之后，需要有类去遵守这个协议，实现协议里的方法。

遵守协议，即在.h文件的父类名后写上<协议名>。实现协议里的方法即在.m文件中实现协议中的方法。相当于给这个类添加了若干个方法。这个类的实例就可以调用这些方法。

如何使用代理呢？通常情况下，我们用delegate，新建文件，选择Objective-C protocal，名字为 MarrayDelegate

内容如下：

    @protocol MarrayDelegate <NSObject>
    @required
    - (void)makeMoney;
    @optional
    - (void)washcloth;
    @end

之前的MarrayProtocol.h文件不要了。

创建Husband类， Husband.h内容如下：

    #import "MarrayDelegate.h"
    @interface Husband : NSObject <MarrayDelegate>
    @end

MarrayDelegate 遵守协议。

Husband.m文件内容如下：

    @implementation Husband
    - (void)makeMoney
    {
        NSLog(@"给老婆挣钱！");
    }
    @end

实现代理里面的方法。

创建Wife类，Wife.h文件内容如下：


    #import "MarrayDelegate.h"
    @interface Wife : NSObject
    @property (nonatomic, assign) id<MarrayDelegate>delegate;
    - (void)shopping;
    @end


将代理名称设为delegate，添加一个方法。

Wife.m文件内容如下：


    @implementation Wife
    - (void)shopping
    {
        NSLog(@"老公，我要购物！");
        [_delegate makeMoney];
    }
    @end


设置代理，和实现方法。

在主函数import husband.h 和 wife.h 文件
    
    Husband *husband = [[Husband alloc] init];
    Wife *wife = [[Wife alloc] init];
    wife.delegate = husband;
    [wife shopping];
    [wife release];
    [husband release];

输出：

    老公，我要购物！
    给老婆挣钱！


整个过程就是实现协议代理的过程。

