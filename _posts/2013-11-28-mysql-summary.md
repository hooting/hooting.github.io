---
layout: post
title: "mysql 小总结"
subtitle: ""
category: "Mysql"
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [Mysql]
---

1、两个关联表的字段的数据需相同，这么同步

    UPDATE tb1 f left join tb2 m on f.uid=m.uid set f.images_ischeck = m.images_ischeck where f.images_ischeck != m.images_ischeck
    UPDATE server_message m left join server_message_schedule s on m.server_id=s.server_id set s.server_cost = m.server_cost

2、给两个关联表的相关字段加索引

    CREATE INDEX idx_origin_id_id ON en_sentence_translated(origin_id,id)

3、两个关联表，其中一个表示一对多的关系，用max、group by

    SELECT cn.content cn_content,en.content en_content,MAX(en.id) FROM `cn_sentence_origin` cn
    LEFT JOIN en_sentence_translated en ON cn.id=en.origin_id 
    WHERE cn.file_id=628 GROUP BY cn.id

4、给数据库加权限

    GRANT ALL ON weblog.* TO wqylog@127.0.0.1 IDENTIFIED BY "xuping";

用户名：wqylog  密码：xuping

5、mysql 清空表

    truncate table 表名;

6、utf-8字符排序

    SELECT * FROM `origin_file` WHERE parentid=0 ORDER BY CONVERT( name USING gbk ) COLLATE gbk_chinese_ci ASC

7、根据IP排序

    SELECT FROM server_message ORDER BY INET_ATON(server_message.server_ip1)

8、根据ids 排序

    select * from server_message m  where m.server_id in(4,5,2,8) order by field(m.server_id,'4,5,2,8')

9、重置mysql密码的步骤

    /usr/local/mysql/bin/mysqld_safe --skip-grant-tables &
    mysql -u root mysql
        mysql>use mysql;
        mysql>update user set password=password("abcdef") where user="root";
        mysql>flush privileges;
    service mysqld restart

10、导出数据库
    
    mysqldump -u 用户名 -p 数据库名 > 导出的文件名
    例如：mysqldump -u root -p news > news.sql

11、导入数据库

    mysql>use 目标数据库名;
    mysql>source 导入的文件名;

