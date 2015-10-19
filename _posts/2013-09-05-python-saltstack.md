---
layout: post
title: "saltstack 学习小记"
subtitle: ""
category: Python
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [Python,saltstack]
---

saltstack的基本介绍

salt是一个新的基础平台管理工具。很短的时间即可运行并使用起来, 扩展性足以支撑管理上万台服务器，数秒钟即可完成数据传递. 经常被描述为 Func加强版+Puppet精简版。

salt的安装

首先添加RPM源:

    rpm -ivh http://mirrors.sohu.com/fedora-epel/6/x86_64/epel-release-6-8.noarch.rpm

1、安装服务端

    yum install salt-master

可以修改配置文件，vim /etc/salt/master 将auto_accept 改为 True，这样可以自动接收客户端

    file_roots:
        base:
            - /srv/salt

这个是可以控制上传的路径

启动服务端： 
	
	/etc/init.d/salt-minion start

2、安装客户端

    yum install salt-minion

修改配置文件，vim /etc/salt/minion master:xxxxxxx   id:xxxxxx

启动客户端：/etc/init.d/salt-minion start

3、salt命令

salt-key -L  列表

salt '*' grains.ls  查看grains分类

salt '*' grains.items 查看grains所有信息

salt '*' grains.item osrelease 查看grains某个信息

命令啥的，就不解释了，参考<a target="_blank" href="http://wiki.saltstack.cn/modules/all">salt内置执行模块列表</a>

4、相关文档 
<a target="_blank" href="http://wiki.saltstack.cn/">saltstack社区</a>

5、salt开发

python开发

    import salt.client
    client = salt.client.LocalClient()
    ret = client.cmd('*', 'cmd.run', ['ls -l'])
    print ret


6、<a href="http://www.ituring.com.cn/article/41632">SaltStack中的文件服务器</a>
