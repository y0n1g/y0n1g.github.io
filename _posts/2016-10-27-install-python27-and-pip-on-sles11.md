---
layout: post
title: 在SLES11上安装python2.7和pip
category: python
---
{% include JB/setup %}

# [在SLES11上安装python2.7]
这个脚本来自stackoverflow

    #!/bin/bash
    # Install Python 2.7.12 alternatively
    zypper install -t pattern sdk_c_c++ -y
    zypper install readline-devel openssl-devel gmp-devel ncurses-devel gdbm-devel zlib-devel expat-devel libGL-devel tk tix gcc-c++ libX11-devel glibc-devel bzip2 tar tcl-devel tk-devel pkgconfig tix-devel bzip2-devel sqlite-devel autoconf db4-devel libffi-devel valgrind-devel -y

    mkdir tmp
    cd tmp
    wget --no-check-certificate http://python.org/ftp/python/2.7.12/Python-2.7.12.tgz
    tar xvfz Python-2.7.12.tgz
    cd Python-2.7.12
    ./configure --prefix=/opt/python2.7 --enable-shared
    make
    make altinstall
    echo "/opt/python2.7/lib" >> /etc/ld.so.conf.d/opt-python2.7.conf
    ldconfig
    cd ..
    cd ..
    #rm -rf tmp


# [安装pip](https://www.fir3net.com/Programming/Python/how-to-install-easyinstall-27-and-pip-27.html)

    wget --no-check-certificate http://pypi.python.org/packages/source/d/distribute/distribute-0.6.35.tar.gz
    tar xf distribute-0.6.35.tar.gz
    cd distribute-0.6.35
    python2.7 setup.py install
    easy_install-2.7 pip
 
