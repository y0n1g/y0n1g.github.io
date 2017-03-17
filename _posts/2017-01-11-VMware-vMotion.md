---
title: VMware vMotion
layout: post
category: [tech]
tags: [VMware, vMotion]
---
{% include JB/setup %}
VMware vMotion有以下一些技术和特点:
1. host之间共享存储, 不同host可以看到同一个虚机的vmdk文件.
1. 虚机的内存和运行状态通过高速网络可以从一个host传输到另一个host. 当基于位图的拷贝结束时, 源VM会暂停, 把最新的内存和运行状态拷贝到新host,在新host上启动虚机, 这个过程不超过2秒.
1. 虚机的网络状态也会保持. 迁移结束时,vMotion会ping网关,告知物理位置的变化.



