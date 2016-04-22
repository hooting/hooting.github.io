---
layout:     post
title:      "Python相关的安装笔记"
subtitle:   ""
date:       2016-04-22 12:00:00
author:     "Hooting"
header-img: "img/post-bg-2015.jpg"
tags:
    - python
    - install
---

## windows下安装pip
 - 在[pip网站](https://pip.pypa.io/en/latest/installing/)下载文件[get-pip.py](https://bootstrap.pypa.io/get-pip.py)。
 - 执行命令：
 > python get-pip.py 

## pip 的使用

 - 安装包：
 > pip install package-name
 - 升级包:
 > pip install package-name --upgrade
 - 删除包:
 > pip uninstall package-name

 ## 检查安装包的版本
 ### 通过pip命令
 > pip show YOUR_PACKAGE_NAME | grep Version
 
 ### 通过python代码
 > print pymysql.VERSION

## python 连接MySQL数据库
可用两个包，分别是[pymysql](https://github.com/PyMySQL/PyMySQL)或者MySQLdb连接数据库。

### pymysql安装
> pip install PyMySQL

### pymysql示例
```python
import pymysql

conn = pymysql.connect(host='127.0.0.1', port=3306,
                       user='root', passwd='123456',
                       db='test',use_unicode=1,charset="utf8")
try:
    with conn.cursor() as cursor:
        # Drop table if it already exist using execute() method.
        cursor.execute("DROP TABLE IF EXISTS EMPLOYEE")
        #create table
        sql = """CREATE TABLE EMPLOYEE (
            FIRST_NAME  CHAR(200) NOT NULL,
            LAST_NAME  CHAR(20),
            AGE INT,
            SEX CHAR(1),
            INCOME FLOAT )"""
        cursor.execute(sql)

    with conn.cursor() as cursor:
        sql = "INSERT INTO EMPLOYEE(FIRST_NAME, \
            LAST_NAME, AGE, SEX, INCOME) \
            VALUES ('%s', '%s', '%d', '%c', '%d' )" % \
            ('Mac', 'Mohan', 20, 'M', 2000)
        cursor.execute(sql)
        conn.commit()
finally:
    conn.close()
```

### MySQLdb安装
> pip install MySQL-python

### MySQLdb示例
```python
import MySQLdb

# Open database connection
db = MySQLdb.connect("localhost","root","123456","test" )

# prepare a cursor object using cursor() method
cursor = db.cursor()

# Drop table if it already exist using execute() method.
cursor.execute("DROP TABLE IF EXISTS EMPLOYEE")

# Create table as per requirement
sql = """CREATE TABLE EMPLOYEE (
         FIRST_NAME  CHAR(200) NOT NULL,
         LAST_NAME  CHAR(20),
         AGE INT,
         SEX CHAR(1),
         INCOME FLOAT )"""

cursor.execute(sql)

# Create table as per requirement
sql = """CREATE TABLE EMPLOYEE (
         FIRST_NAME  CHAR(200) NOT NULL,
         LAST_NAME  CHAR(20),
         AGE INT,
         SEX CHAR(1),
         INCOME FLOAT )"""
#
#cursor.execute(sql)

# Prepare SQL query to INSERT a record into the database.
sql = "INSERT INTO EMPLOYEE(FIRST_NAME, \
       LAST_NAME, AGE, SEX, INCOME) \
       VALUES ('%s', '%s', '%d', '%c', '%d' )" % \
       ('Mac', 'Mohan', 20, 'M', 2000)
# Execute the SQL command
cursor.execute(sql)
# Commit your changes in the database
db.commit()
# Rollback in case there is any error

# disconnect from server
db.close()

```

