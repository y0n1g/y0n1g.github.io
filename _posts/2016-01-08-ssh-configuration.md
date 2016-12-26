---
title: ssh配置
tags : [ssh]
category : [tech]
layout: post
---
{% include JB/setup %}
- 可以指定用某个key访问某些地址。配置文件是 ~/.ssh/config 例如：
> Host 192.168* 10.109.*  dev-* *-lab
> User root
> IdentityFile /var/share/ssh/id_rsa.root
> StrictHostKeyChecking no
