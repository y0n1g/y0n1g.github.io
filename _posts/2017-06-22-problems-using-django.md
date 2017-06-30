---
title: 使用django时遇到的问题
layout: post
category: [tech]
tags: [django,]
---
{% include JB/setup %}

## DateTimeField总是返回None.

环境: SLES12, mysql, apache2

定义了一个DateTimeField字段, 写入没有问题. 用manage.py shell也可以把所有内容正常读出, 使用django自带的development server时admin也可以正常显示这个值.
    
问题是,一旦切换到生产服务器apache, 这个字段永远返回None.
    
在[这里](https://stackoverflow.com/questions/24548902/datetimefield-queryset-returning-none-in-django)找到了一个相同问题的解决方法:
    
    django创建表时使用datetime(6). 需要把这个字段改为datetime:
    ALTER TABLE `my_table` MODIFY COLUMN `created` datetime NOT NULL
    
但是这个方法只是一个workaround,并不是最终解决方案.
    
继续搜索, 在[这里](https://github.com/farcepest/MySQLdb1/issues/24)说MySQL-python-1.2.5有一个bug. 但是实际测试发现, 即使更换了mysqlclient-1.3.10,亦然有问题

