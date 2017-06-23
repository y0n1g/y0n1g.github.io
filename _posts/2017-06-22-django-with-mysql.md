---
title: 在MySQL上使用Django
layout: post
category: [tech]
tags: [django,mysql]
---
{% include JB/setup %}
基于1.11.3的官方文档.
Django 1.11.3.dev20170620152238 documentation

# 版本:
    
Django使用MySQL5.5以上版本. 并且需要其默认支持utf-8.

# 驱动:
    
*推荐驱动*: mysqlclient1.3.3以上, 支持python3.

MySQLdb1.2.3以上. 注意,有已知的bug需要处理.
MySQL Connector/Python 也可以用.

# 时区

如果需要Django支持时区,需要使用[mysql_tzinfo_to_sql](https://dev.mysql.com/doc/refman/en/mysql-tzinfo-to-sql.html)导入.

# 创建数据库:

    CREATE DATABASE <dbname> CHARACTER SET utf8;

# 连接数据库

## 隔离域

在配置部分, OPTION中,'isolation_level' 支持下面四种模式:

+ 'read uncommitted'
+ 'read committed'
+ 'repeatable read'
+ 'serializable'

# 某些数据类型

## 数据强制转换

如果继承自Field,自己实现某个字段类型, 需要实现这几个函数:get_prep_value(), or use RawSQL, extra(), or raw()



