---
layout: post
title: "django1.0.4 blog Admin管理界面的激活方法"
subtitle: ""
category: Python
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [Python]
---

先django-admin startproject mysite创建一个项目
cd mysite
manage.py startapp blog  创建一个blog

1、设置数据库 settings.py

    DATABASE_ENGINE = 'mysql'
    DATABASE_NAME = 'djangodb'
    DATABASE_USER = 'root'
    DATABASE_PASSWORD = ''
    DATABASE_HOST = 'localhost'
    DATABASE_PORT = ''


2、建表

命令行

manage.py syncdb
........

3、设置自动admin应用 settings.py

在django.contrib.auth下添加django.contrib.admin,mysite.blog

4、再次 命令行

manage.py syncdb
........

5、urls.py内容为

    from django.conf.urls.defaults import *

    # Uncomment the next two lines to enable the admin:
    from django.contrib import admin
    admin.autodiscover()

    urlpatterns = patterns('',
        # Uncomment the next line to enable the admin:
        (r'^admin/(.*)', admin.site.root),
    )


注意，这和老版本不太一样，当出现No module named urls时，仔细检查下此文件。

6、然后models.py内容为

    from django.db import models
    from django.contrib import admin

    class BlogPost(models.Model):
        title = models.CharField(max_length=150)
        body = models.TextField()
        timestamp = models.DateTimeField()

    admin.site.register(BlogPost)

7、最后在浏览器运行，http://127.0.0.1:8888/admin/，就可以看到登录页面了。
发布文章的时候，如果发现blog_blogpost表不存在，就再执行下manage.py syncdb