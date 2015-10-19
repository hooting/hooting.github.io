---
layout: post
title: "php opcache使用"
subtitle: ""
category: PHP
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [PHP]
---

**介绍**

php opcache的缓存原理，主要是避免php重复编译，减少CPU和内存开销。

**安装**

到http://pecl.php.net/package/ZendOpcache下载
	
	tar zxvf zendopcache-7.0.5.tgz
	cd zendopcache-7.0.5
	phpize
	./configure
	make && make install

**配置**

修改php.ini，在最后添加

	[Zend Opcache]
	zend_extension = "/usr/local/php-5.4.10/lib/php/extensions/no-debug-non-zts-20100525/opcache.so"
	opcache.force_restart_timeout=3600
	opcache.memory_consumption=1024
	opcache.optimization_level=1
	opcache.interned_strings_buffer=8
	opcache.max_accelerated_files=4096
	opcache.revalidate_freq=60
	opcache.fast_shutdown=1
	opcache.enable=1
	opcache.enable_cli=1
	
**启动**

重启php-fpm或者apache/nginx


**查看**

phpinfo中，Zend OPcache
	
	Cache hits 
	Cache misses

看这两个值

**压力测试**

1、未使用opcache
	
	./ab -n 100 -c 100 http://127.0.0.1/phpinfo.php

	This is ApacheBench, Version 2.3 <$Revision: 655654 $>
	Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
	Licensed to The Apache Software Foundation, http://www.apache.org/

	Benchmarking 127.0.0.1 (be patient).....done

	Server Software:        nginx
	Server Hostname:        127.0.0.1
	Server Port:            80
	
	Document Path:          /phpinfo.php
	Document Length:        56203 bytes
	
	Concurrency Level:      100
	Time taken for tests:   1.849 seconds
	Complete requests:      100
	Failed requests:        4
	   (Connect: 0, Receive: 0, Length: 4, Exceptions: 0)
	Write errors:           0
	Total transferred:      5635595 bytes
	HTML transferred:       5620295 bytes
	Requests per second:    54.08 [#/sec] (mean)
	Time per request:       1848.981 [ms] (mean)
	Time per request:       18.490 [ms] (mean, across all concurrent requests)
	Transfer rate:          2976.51 [Kbytes/sec] received
	
	Connection Times (ms)
	              min  mean[+/-sd] median   max
	Connect:        0    3   0.9      3       4
	Processing:    45 1190 312.7   1137    1805
	Waiting:       39 1182 316.0   1137    1805
	Total:         45 1193 312.3   1141    1807
	
	Percentage of the requests served within a certain time (ms)
	  50%   1141
	  66%   1262
	  75%   1403
	  80%   1482
	  90%   1658
	  95%   1726
	  98%   1759
	  99%   1807
	 100%   1807 (longest request)

2、使用opcache
	
	./ab -n 100 -c 100 http://127.0.0.1/phpinfo.php
 
	This is ApacheBench, Version 2.3 <$Revision: 655654 $>
	Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
	Licensed to The Apache Software Foundation, http://www.apache.org/

	Benchmarking 127.0.0.1 (be patient).....done

	Server Software:        nginx
	Server Hostname:        127.0.0.1
	Server Port:            80
	
	Document Path:          /phpinfo.php
	Document Length:        60436 bytes
	
	Concurrency Level:      100
	Time taken for tests:   0.238 seconds
	Complete requests:      100
	Failed requests:        12
	   (Connect: 0, Receive: 0, Length: 12, Exceptions: 0)
	Write errors:           0
	Total transferred:      6058888 bytes
	HTML transferred:       6043588 bytes
	Requests per second:    419.62 [#/sec] (mean)
	Time per request:       238.312 [ms] (mean)
	Time per request:       2.383 [ms] (mean, across all concurrent requests)
	Transfer rate:          24828.30 [Kbytes/sec] received
	
	Connection Times (ms)
	              min  mean[+/-sd] median   max
	Connect:        0    3   0.8      3       5
	Processing:    11  164  70.1    218     228
	Waiting:        5  164  70.3    217     228
	Total:         11  167  69.5    219     231
	
	Percentage of the requests served within a certain time (ms)
	  50%    219
	  66%    224
	  75%    226
	  80%    227
	  90%    229
	  95%    230
	  98%    231
	  99%    231
	 100%    231 (longest request)

**结论**

使用opcache使php执行效率提高不少。