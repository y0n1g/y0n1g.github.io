---
title: 在apache上使用subprocess调用命令行的异常调试
layout: post
category: [tech]
tags: [django,python]
---
{% include JB/setup %}

目的: apache+django+subprocess, 自动调用uemcli命令.

错误:
如果用了Shell=False, 则报告'Operation failed. Error code: 0x1000002'错误.
如果用了Shell=True, 则报告'Operation failed. Error code: 0x1000002'错误.




调查发现, uemcli是一个sh脚本, 其封装的bin文件需要使用添加'/opt/emc/uemcli/lib'到LD_LIBRARY_PATH.

apache2在SLES12上是以用户wwwrun运行的, 这个用户没有交互式sh.
而uemcli需要在用户的目录下创建.emc目录, 并且将certificate文件放在里面.
