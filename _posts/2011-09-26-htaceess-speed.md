---
layout: post
title: "前端优化，利用.htaceess控制图片，js，css缓存时间提升加载速度"
subtitle: ""
category: HTML
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [HTML]
---

前端优化，利用.htaceess控制图片，js，css缓存时间提升网页加载速度。

Add an Expires or a Cache-Control Header：

简单点说：就是缓存一下不常修改的文件，提升访问速度。

再简单点说：就是在.htaccess文件中写入以下内容：


    <IfModule mod_expires.c>
        ExpiresActive On
        ExpiresDefault A600
        ExpiresByType image/x-icon A2592000
        ExpiresByType application/x-javascript A2592000
        ExpiresByType text/css A604800
        ExpiresByType image/gif A2592000
        ExpiresByType image/png A2592000
        ExpiresByType image/jpeg A2592000
        ExpiresByType text/plain A86400
        ExpiresByType application/x-shockwave-flash A2592000
        ExpiresByType video/x-flv A2592000
        ExpiresByType application/pdf A2592000
        ExpiresByType text/html A600
    </IfModule> 

解释一下。ExpiresByType 是通过MIME类型来设置具体文件的缓存时间，A表示访问，A后面的数字表示访问后的缓存时间。AddOutputFilterByType表示根据后面 的MIME类型来压缩文件，这里对css，html，gif，jpeg，png，JavaScript等进行gzip压缩。
更多选项请参考apache手册吧。
