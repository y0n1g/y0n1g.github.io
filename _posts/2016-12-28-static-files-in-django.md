---
title: django的静态文件
layout: post
category: [tech]
tags: [django,python,web]
---
{% include JB/setup %}
一般生产环境建议使用django服务static文件,而是由web server来直接提供服务.  
如果在开发阶段要用django的静态文件服务的话, 顺序如下:

1. 确保settigns中安装了服务:

    django.contrib.staticfiles 

1. 定义静态文件的url

    STATIC_URL = '/static/'

1. 定义静态文件的目录

    STATICFILES_DIRS = [
        os.path.join(BASE_DIR, "static"),
        '/var/www/static/',
    ]
    _这种结构将使static文件和template的目录结构使用方法一样_
    
