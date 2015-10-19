---
layout: post
title: "Redis 浅见"
subtitle: ""
category: Redis
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [Redis]
---

以前只会安装redis，和使用string类型，这个星期，为了使自己更懂一点，所以看了很多关于redis的文章。所以写了这篇浅谈博客，有观点不准确的地方，可以纠正我。

在NoSql领域中，redis占据着重要位置。她小儿精悍，被比喻作AK-47。

下面要讨论的是redis的数据类型，关于aof日志文件，以及rediska的使用。

Redis的数据类型Strings, integers, lists, hashes, sets, sorted sets

他们的用法如下：

    1、Strings

    > set foo bar
    OK
    >get foo
    "bar"

    2、integer

    >set mykey "10"
    OK 
    >incr mykey
    (integer) 11
    >get mykey
    "11"
    >decr mykey
    (integer) 10
    >get mykey
    "10"


    3、Lists

    >rpush foo baz
    (integer) 1
    >rpush foo qux
    (integer) 2
    >lpush foo bar
    (integer) 3
    >lrange foo 0 -1
    "bar"
    "baz"
    "qux"
    >lrange foo 0 1
    "bar"
    "baz"
    >lpop foo
    "bar"
    >rpop foo
    "qux"

    4、Hashes

    >hset foo bar baz
    (integer) 1
    >hset foo qux quxx
    (integer) 1
    >hget foo bar
    "baz"
    >hgetall foo
    "bar"
    "baz"
    "qux"
    "quxx"

    5、Set

    >sadd m1 jan
    (integer) 1
    >sadd m1 feb
    (integer) 1
    >sismember m1 jan
    (integer) 1
    >sismember m1 feb
    (integer) 1
    >smembers m1
    "feb"
    "jan"
    >sadd m2 feb
    (integer) 1
    >sismember m2 jan
    (integer) 1
    >sismember m2 mar
    (integer) 1
    >sinter m1 m2
    "feb"
    >sdiff m1 m2
    "jan"
    >sunion m1 m2
    "mar"
    "jan"
    "feb"

    6、sorted sets

    >zadd z1 1 jan
    (integer) 1
    >zadd z1 2 feb
    (integer) 1
    >zadd z1 3 mar
    (integer) 1
    >zscore z1 feb
    "2"
    >zrange z1 0 1 withscores
    "jan"
    "1"
    "feb"
    "2"
    "mar"
    "3"


由上可知，我觉得理想的存储数据类型是lists和hashes。

昨天晚上翻了一遍算法导论的数据结构那一张。Lists相当于队列，可以lpush，rpush，lpop，rpop。Hashes相当于指针指向一块，这一块里可以包含很多数据。

rediska，使用rediska来代替phpredis模块。

用法很简单，如下：

    require 'Rediska.php';
    $rediska = new Rediska();
    $rediska ->connect('127.0.0.1',6379);
    $rediska ->set('test','hello world!');
    echo $rediska ->get('test');