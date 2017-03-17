---
title: VMware 存储迁移技术: storage vmotion
layout: post
category: [tech]
tags: [VMware, svmotion]
---
{% include JB/setup %}
VMware的storage vmotion,是一种虚机磁盘迁移技术.它可以在虚机运行时将虚机的后端磁盘迁移到其他存储设备,以便进行升级或存储替换等业务操作.  

迁移时,可以将所有磁盘放在一个存储上,也可以将不同的磁盘放在不同的存储上. 迁移的时候,vm所在的host是不变的.


