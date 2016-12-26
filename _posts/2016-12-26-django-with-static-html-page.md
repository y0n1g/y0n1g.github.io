---
title: 在django中使用静态页面
layout: post
category: [tech]
tags: [django,python]
---
{% include JB/setup %}
为了提升效率,django使用django.contrib.flatpages这个包来处理静态页面. 这个包依赖于django.contrib.sites. 首先介绍sites.  

# sites
sites是django的多站点框架.  

为了使用sites,需要为model增加一些约束条件. 达到的结果是, 同一个model可以在多个不同的site上复用. 原因是各种site都有一些同样类似的功能, 或者需要包含同样的数据; 同时在使用或显示时, 又需要根据不同site有一点差异.  
  
sites定义的Site模型,包含domain和name两个字段. SITE_ID 设置指定了与特定配置文件相关联的 Site 对象之数据库 ID.  
  
使用sites这个包时,需要创建数据库表. 导致的结果是,数据库中有当前的project支持的各个网站. 对于不同的Model,添加一个site字段, 可以指明这个Model是跟哪些site关联的.

添加sites这个app后,运行migrate创建数据库表,将会创建一个默认的example.com这个site.

**使用sites,重点是要考虑哪些model是需要在多个site之间共享的.**  

# flatpages
flatpages是django的简单页面包.
