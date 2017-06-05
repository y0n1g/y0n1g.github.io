---
title: 在SLES11上配置django和apache2服务器一起工作
layout: post
category: [tech]
tags: [Suse,apache,django]
---
{% include JB/setup %}
# note:
首先声明, 不建议在SLES11上使用django. 因为SLES11原生系统是python2.6, 而django已经不再支持2.6了.

Suse的默认环境中,包含了apache2和wsgi.也有django包. 但是配置起来,比ubuntu要麻烦很多.
原因在于suse的apache默认是使用virtual host模块. python还不能配置在virtual host中. 因此有不少坑需要填...
suse还没有关于wsgi部分的设置文档...


# 初始环境:
1. django的项目已经创建准备使用
1. apache2已经安装可以显示默认页面
1. 确认apache的wsgi模块已经安装. sles11上配置apache模块的命令是a2enmod/a2dismod. suse维护的,文档不怎么样,真心不好用.

    h@sles11:/$ a2enmod -q wsgi
    h@sles11:/$
    #什么都不显示, 就表示模块已经安装... 这是什么狗屁ui嘛. a2enmod是个shell脚本,可以看看.
   
# 坑
虽然各种设置都跟ubuntu上面的一样, 但是还是无法使用wsgi.
最终找到了一篇[文档](https://moinmo.in/HowTo/SUSELinuxEnterpriseQuick), 提到了需要编辑/etc/sysconfig/apache2 文件的APACHE_MODULES参数,需要将wsgi添加在里面.
其他设置,参考ubuntu上面的[设置方法](http://y0n1g.github.io/2016/12/apache-with-django-in-ubuntu)

1. 提示如下错误: 

                fasttypes = {int, str, frozenset, type(None)},
                                ^
    SyntaxError: invalid syntax


可能的原因:

    1. python的2.6不支持.一定要注意一下本地的python版本是系统原生的还是后来安装的. SLES11系统原生是python2.6, 对应的mod_wsgi也是针对python2.6的. 现在django已经不再支持2.6的python了, 所以需要找到匹配的django
    1. django旧版本不对

