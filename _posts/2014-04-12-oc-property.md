---
layout: post
title: "OC property 属性介绍"
subtitle: ""
category: OC
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [iOS,OC]
---

    @property (nonatomic, retain) NSString *subject;

@propery属性有 atomic, nonatomic, assign, retain, copy, 作用和系统内部实现如下

1、 atomic, nonatomic 原子性和非原子性。
    
在原子性控制上，我们通常设置为nonatomic。

程序开发过程中，setter、getter处处都在用，如果使用atomic，需要不断的对setter、getter加锁解锁以保证线程访问安全，会很占用系统资源，降低系统性能。

通常设置为nonatomic，某些属性需要线程安全的时候，才定义为atomic。


2、 assign 内部实现

    - (void)setSubject:(NSString *)subject
    {
        _subject = subject;
    }
    - (NSString *)subject
    {
        return _subject;
    }

3、 retain 内部实现

    - (void)setSubject:(NSString *)subject
    {
        if (_subject != subject) {
            [_subject release];
            _subject = [subject retain];
        }
    }

    - (NSString *)subject
    {
        return [[_subject retain] autorelease];
    }

4、 copy 内部实现

    - (void)setSubject:(NSString *)subject
    {
        if (_subject != subject) {
            [_subject release];
            _subject = [subject copy];
        }
    }

    - (NSString *)subject
    {
        return [[_subject retain] autorelease];
    }


总结：
    基本数据类型等用assign，其他对象用retain或者copy，当用MRC时，需要在dealloc中release。


用self和不用self时，引用计数的区别

    - (void)viewDidLoad
    {
        [super viewDidLoad];
        NSArray * array = [[NSArray alloc] initWithObjects:@"Luke", @"Leia", @"Han", @"Chewbacca", @"Artoo", @"Threepio", @"Lando", nil];
        self.pickerData= array;
        [array release]; // 如果不加self.就会在这里把array直接完全释放，结果array就没法用了。
    }


简述内存管理的原则.

答: 凡是你通过retain、alloc、copy等获得了所有权的对象，都必须在你不再使用它的时候，由你来release、autorelease等手段来释放对它的所有权。retain、alloc、copy的操作次数应该与release、autorelease操作的次数相等。可以在类的dealloc方法中释放你所占有的实例变量。对于便利构造器和访问器来说，你没有通过retain、alloc、copy获得对象的所有权，因此在这些情况下你无须对获得对象进行额外的释放操作。autorelease只不过意味着“预定延迟发送一条release”，当前引用计数并没有变。


