---
title: "apache的一些常见配置项"
layout: post
category: [tech]
tags : [apache,http]
---
{% include JB/setup %}
# apache 的一些常见配置项

#### 启动目录列表
一般是针对某一个具体的目录，参考如下：
比如，对于ubuntu来说，其apache的配置目录在/etc/apache2. 
其中，httpd.conf包含了各种默认设置，可以作为学习的起点。但是不要在这里做修改。
如果没有启动虚拟主机，最简单的方法是在default-server.conf文件中加入如下几行：

    <Directory "/home/http/list_this_directory">
        Options +Indexes
    </Directory>
