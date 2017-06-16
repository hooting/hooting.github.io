---
layout:     post
title:      "如何创建一个不可变的对象"
subtitle:   "附不可变对象的优点"
date:       2015-03-13 12:00:00
author:     "Hooting"
header-img: "img/post-bg-2015.jpg"
tags:
    - object
    - java
---

## 不可变类的定义
一个不可变的类，他的状态在创建后就不可以再被改变。状态包括了对象里的基本类型变量值和
引用类型变量值。

不可变类的优势有如下几点：
* 易于构造、测试、使用
* 保证线程安全，且不需要关心同步问题
* 不需要 **copy** 构造器
* 不需要实现 **clone**
* allow hashCode to use lazy initialization, and to cache its return value
  ** 不明白 **
* 可以成为 **map** 的 **key**, 或是 **set** 的元素
*

为了保证一个类是不可变的，需要遵循以下几点：
* 不要提供 **setter** 方法
* 设置所有属性的修饰符为 **final** 和 **private**
* 不要让子类重载方法。一个简单粗暴的方法是：把类设置为 **final**
* 不可变里的属性，分清楚哪些是不可变类型，哪些不是。对于可变的属性，在返回时，应该拷贝
  一份，把新的对象返回给调用者

以下是一个不可变类的例子:

```java
import java.util.Date;

/**
* Always remember that your instance variables will be either mutable or immutable.
* Identify them and return new objects with copied content for all mutable objects.
* Immutable variables can be returned safely without extra effort.
* */
public final class ImmutableClass
{

    /**
    * Integer class is immutable as it does not provide any setter to change its content
    * */
    private final Integer immutableField1;
    /**
    * String class is immutable as it also does not provide setter to change its content
    * */
    private final String immutableField2;
    /**
    * Date class is mutable as it provide setters to change various date/time parts
    * */
    private final Date mutableField;

    //Default private constructor will ensure no unplanned construction of class
    private ImmutableClass(Integer fld1, String fld2, Date date)
    {
        this.immutableField1 = fld1;
        this.immutableField2 = fld2;
        this.mutableField = new Date(date.getTime());
    }

    //Factory method to store object creation logic in single place
    public static ImmutableClass createNewInstance(Integer fld1, String fld2, Date date)
    {
        return new ImmutableClass(fld1, fld2, date);
    }

    //Provide no setter methods

    /**
    * Integer class is immutable so we can return the instance variable as it is
    * */
    public Integer getImmutableField1() {
        return immutableField1;
    }

    /**
    * String class is also immutable so we can return the instance variable as it is
    * */
    public String getImmutableField2() {
        return immutableField2;
    }

    /**
    * Date class is mutable so we need a little care here.
    * We should not return the reference of original instance variable.
    * Instead a new Date object, with content copied to it, should be returned.
    * */
    public Date getMutableField() {
        return new Date(mutableField.getTime());
    }

    @Override
    public String toString() {
        return immutableField1 +" - "+ immutableField2 +" - "+ mutableField;
    }
}
```

[原文链接](http://howtodoinjava.com/core-java/interviews-questions/how-to-make-a-java-class-immutable/)
