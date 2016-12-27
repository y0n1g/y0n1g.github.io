---
title: 用python访问mysql
layout: post
category: [tech]
tags: [python, mysql]
---
{% include JB/setup %}
# 准备连接器
## mysql的官方连接器
安装好python和mysql后, 需要安装 mysql-connector-python. 可以在[这里下载](http://dev.mysql.com/downloads/connector/python/)

遇到一个问题是,虽然我已经在SLES11.sp2上安装了python2.7.12, 但是还是无法安装正确的connector, 提示如下:

        $rpm -ivh mysql-connector-python-2.0.5-1.sles12.noarch.rpm
        warning: mysql-connector-python-2.0.5-1.sles12.noarch.rpm: Header V3 DSA signature: NOKEY, key ID 5072e1f5
        error: Failed dependencies:
        python(abi) = 2.7 is needed by mysql-connector-python-2.0.5-1.sles12.noarch
    
后来经过搜索发现本地的rpm -q python显示安装的是python-2.6.8-0.15.1. 简单的解决方法是安装老版本的connector.... 这个方法安装的rpm真的能用么?
最后的解决方法,是安装了几个软件包,包括上述的connector. 加入参数--nodeps 可以解决版本依赖检查(实际使用时不会受影响)

    rpm -ivh --nodeps mysql-connector-python-2.0.5-1.sles12.noarch.rpm
    warning: mysql-connector-python-2.0.5-1.sles12.noarch.rpm: Header V3 DSA signature: NOKEY, key ID 5072e1f5
    Preparing...                ########################################### [100%]
    1:mysql-connector-python ########################################### [100%]
   
还有这几个软件包也一并装上:

    zypper install libmysqlclient-devel mysql-tools python-mysql
    

## MySQLdb
这个项目在[sourceforgeg](https://sourceforge.net/projects/mysql-python/)和[github](https://github.com/farcepest/MySQLdb1)上都有维护
但是在linux上的名字不一样.python官网包名为[mysql-python](https://pypi.python.org/pypi/MySQL-python/).安装python的软件包:

    pip install mysql-python

## 其他API
其实还有很多API可以用.

    MySQL-Python
    PyMySQL
    MySQL Connector/Python
    CyMySQL
    OurSQL
    Google Cloud SQL
    PyODBC
    zxjdbc for Jython
    
