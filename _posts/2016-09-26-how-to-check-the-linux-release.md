---
layout: post
title: 如何识别当前的linux版本？
category: [linux]
---
{% include JB/setup %}


# 内核版本：
uname -a

# 发行商：
lsb_release -a

# 确定某个软件的版本：
基于debian：
dpkg -l openssl
基于rpm:
rpm -qa | grep openssl
