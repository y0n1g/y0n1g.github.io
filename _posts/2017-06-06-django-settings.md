---
title: Django的设置
layout: post
category: [tech]
tags: [django,]
---
{% include JB/setup %}
Django的设置文件settings.py,是在创建project时自动生成的.

官方文档在这里:

* [官方介绍文档1.11](https://docs.djangoproject.com/en/1.11/topics/settings/)
* [官方详细文档1.11](https://docs.djangoproject.com/en/1.11/ref/settings/)

在实际使用中,需要注意的一些变量:


# TIME_ZONE

    默认值是 'UTC'. 这个值得设置应该跟当前的服务器的timezone一致.而很可能服务器设置的时区不是UTC.
    比如在SLES12系统上,查看当前时间:
    # timedatectl
          Local time: Tue 2017-06-06 00:14:02 EDT
      Universal time: Tue 2017-06-06 04:14:02 UTC
            RTC time: Tue 2017-06-06 04:14:02
            Timezone: America/New_York (EDT, -0400) <---- 当前的时区
         NTP enabled: yes
    NTP synchronized: yes
     RTC in local TZ: no
          DST active: yes
     Last DST change: DST began at
                      Sun 2017-03-12 01:59:59 EST
                      Sun 2017-03-12 03:00:00 EDT
     Next DST change: DST ends (the clock jumps one hour backwards) at
                      Sun 2017-11-05 01:59:59 EDT
                      Sun 2017-11-05 01:00:00 EST
    则应该这样设置:
    TIME_ZONE = 'America/New_York'

    在ubuntu16.04上
    h@ubuntu:~$ date
    Sun Jun 11 18:55:17 CST 2017
    h@ubuntu:~$ more /etc/timezone
    Asia/Shanghai
    则应该这样设置:
    TIME_ZONE = 'Asia/Shanghai'



这个值得设置很重要.具体可以参考django官方文档关于[Settings](https://docs.djangoproject.com/en/1.11/ref/settings/)


# DATABASES


