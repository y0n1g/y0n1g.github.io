---
title: 用python访问mysql
layout: post
category: python, mysql
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

## MySQLdb
这个项目在[sourceforgeg](https://sourceforge.net/projects/mysql-python/)和[github](https://github.com/farcepest/MySQLdb1)上都有维护
