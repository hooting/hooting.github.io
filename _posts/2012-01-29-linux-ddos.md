---
layout: post
title: "实战DoS Deflate"
subtitle: ""
category: Linux
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [Linux]
---

DDoS deflate是一款免费的用来防御和减轻DDoS攻击的脚本。它通过netstat监测跟踪创建大量网络连接的IP地址，在检测到某个结点超过预设的限 制时，该程序会通过APF或IPTABLES禁止或阻挡这些IP.</p>

DDoS deflate官方网站：http://deflate.medialayer.com/

    netstat -ntu | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -n

执行上面的命令后，

    1 Address
    1 servers)
    2 123.113.152.60
    4 59.151.36.148
    ................................

每个IP几个、十几个或几十个连接数都还算比较正常，如果像上面成百上千肯定就不正常了。

好了，下面就安装DDoS deflate吧。

第一步：安装DDoS deflate

    wget http://www.inetbase.com/scripts/ddos/install.sh
    chmod 0700 install.sh
    ./install.sh    //安装

卸载：

    wget http://www.inetbase.com/scripts/ddos/uninstall.ddos
    chmod 0700 uninstall.ddos
    ./uninstall.ddos

第二步：配置DDoS deflate

下面是DDoS deflate的默认配置位于/usr/local/ddos/ddos.conf ，内容如下：

    ##### Paths of the script and other files
    PROGDIR="/usr/local/ddos"
    PROG="/usr/local/ddos/ddos.sh"
    IGNORE_IP_LIST="/usr/local/ddos/ignore.ip.list"  //IP地址白名单
    CRON="/etc/cron.d/ddos.cron"    //定时执行程序
    APF="/etc/apf/apf"
    IPT="/sbin/iptables"

    ##### frequency in minutes for running the script
    ##### Caution: Every time this setting is changed, run the script with --cron
    #####          option so that the new frequency takes effect
    FREQ=1   //检查时间间隔，默认1分钟

    ##### How many connections define a bad IP? Indicate that below.
    NO_OF_CONNECTIONS=150     //最大连接数，超过这个数IP就会被屏蔽，一般默认即可

    ##### APF_BAN=1 (Make sure your APF version is atleast 0.96)
    ##### APF_BAN=0 (Uses iptables for banning ips instead of APF)
    APF_BAN=1        //使用APF还是iptables。推荐使用iptables,将APF_BAN的值改为0即可。

    ##### KILL=0 (Bad IPs are'nt banned, good for interactive execution of script)
    ##### KILL=1 (Recommended setting)
    KILL=1   //是否屏蔽IP，默认即可

    ##### An email is sent to the following address when an IP is banned.
    ##### Blank would suppress sending of mails
    EMAIL_TO="root"   //当IP被屏蔽时给指定邮箱发送邮件，推荐使用，换成自己的邮箱即可

    ##### Number of seconds the banned ip should remain in blacklist.
    BAN_PERIOD=600    //禁用IP时间，默认600秒，可根据情况调整

用户可根据给默认配置文件加上的注释提示内容，修改配置文件。

第三步：查看/usr/local/ddos/ddos.sh文件的第117行

    netstat -ntu | awk ‘{print $5}’ | cut -d: -f1 | sort | uniq -c | sort -nr > $BAD_IP_LIST

修改为以下代码即可！

    netstat -ntu | awk ‘{print $5}’ | cut -d: -f1 | sed -n '/[0-9]/p' | sort | uniq -c | sort -nr > $BAD_IP_LIST



OK了！