---
title: MySQL 安装与配置
layout: post
category: [tech]
tags: [database, mysql]
---
{% include JB/setup %}
# 环境:
SLES11.SP2

# 安装:
很简单

    $ zypper install mysql

# 配置
首先要配置root的密码. 在第一次启动mysql的服务时,会提有如下提示:

    PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !
    To do so, start the server, then issue the following commands:
    /usr/bin/mysqladmin -u root password 'new-password'
    /usr/bin/mysqladmin -u root -h your_local_host_name password 'new-password'
    Alternatively you can run:
    /usr/bin/mysql_secure_installation
    which will also give you the option of removing the test
    databases and anonymous user created by default.  This is
    strongly recommended for production servers.
    See the manual for more instructions.
    You can start the MySQL daemon with:
    rcmysql start
    You can test the MySQL daemon with mysql-test package
    Please report any problems with the /usr/bin/mysqlbug script!
    Starting service MySQL                                      

注意,如果在ubuntu上用apt-cache安装,默认不允许root在localhost登录

    ERROR 1698 (28000): Access denied for user 'root'@'localhost'
    
# 开始使用:
现在就可以用mysql进入交互式界面了:

    $ mysql -u root -p

一般要创建操作用户,而不是每次用root登录

    mysql> CREATE USER your_name IDENTIFIED BY 'Password';

创建数据库:

    mysql>  CREATE DATABASE your_database CHARACTER SET utf8;

赋予用户权限:

    mysql> GRANT ALL on DATABASE.* to your_name;


