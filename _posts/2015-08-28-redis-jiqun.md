---
layout: post
title: "Redis 集群"
subtitle: ""
category: Redis
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [Redis]
---

**介绍**

redis 集群部署，针对两台以上服务器的部署，一个主服务器，多个从服务器。

**安装**

    tar zxvf redis-2.8.12.tar.gz
    cd redis-2.8.12
    make

创建文件夹并且拷贝

    mkdir /usr/redis

    cp redis.conf /usr/redis
    cd src
    cp redis-server /usr/redis
    cp redis-cli /usr/redis
    cp redis-benchmark /usr/redis
    cp mkreleasehdr.sh /usr/redis
    cp redis-check-aof /usr/redis
    cp redis-check-dump /usr/redis

修改redis.conf文件 daemonize no 改为 yes # 是否后台运行

复制启动文件

    cp redis-2.8.12/utils/redis_init_script /etc/init.d/redis

修改某些路径后。加入chkconfig

    chkconfig --add redis
    service redis start/stop #启动/关闭

修改 vim ~/.bash_profile

添加一行 
    
    PATH=$PATH:/usr/redis
    source ~/.bash_profile #重启

就能启动 redis-cli 了

**主从分配**
    
主服务器开启192.168.1.2:6379端口

从服务器修改redis.conf配置文件
    
    slave-read-only no
    slaveof 192.168.1.2 6379

**PHP程序主从**
    
具体的PHP如何主从调用，请看<a href="https://github.com/xpmozong" target="_blank">wavephp</a>框架

**未来架构设想**

Redis多集群，多服务器，多从服务器部署，采用zookeeper架构