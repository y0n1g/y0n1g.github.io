---
title: 如何识别当前的linux版本？
layout: post
category: [tech]
tags: [linux]
---
{% include JB/setup %}


# 内核版本：

    h@ubuntu:~$ uname -a
    Linux ubuntu 4.4.0-57-generic #78-Ubuntu SMP Fri Dec 9 23:46:51 UTC 2016 i686 i686 i686 GNU/Linux
    

# 发行商：

    h@ubuntu:~$ lsb_release -a
    No LSB modules are available.
    Distributor ID: Ubuntu
    Description:    Ubuntu 16.04.1 LTS
    Release:        16.04
    Codename:       xenial
    

# 确定某个软件的版本：
+ 基于debian：

    h@ubuntu:~$ dpkg -l openssl
    Desired=Unknown/Install/Remove/Purge/Hold
    | Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
    |/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
    ||/ Name           Version      Architecture Description
    +++-==============-============-============-=================================
    ii  openssl        1.0.2g-1ubun i386         Secure Sockets Layer toolkit - cr
    
+ 基于rpm:

    [root@my_host ~]# rpm -qa | grep openssl
    openssl098e-0.9.8e-18.el6_5.2.x86_64
    openssl-devel-1.0.1e-16.el6_5.15.x86_64
    krb5-pkinit-openssl-1.10.3-15.el6_5.1.x86_64
    openssl-1.0.1e-16.el6_5.15.x86_64

