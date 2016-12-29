---
title: 在ubuntu上配置django和apache2服务器一起工作
layout: post
category: [tech]
tags: [ubuntu,apache,django]
---
{% include JB/setup %}
# 初始环境:
1. django的项目已经创建准备使用
1. apache2已经安装可以显示默认页面
1. 确认apache的wsgi模块已经安装

    h@ubuntu:/$ apt-cache pkgnames | grep libapache2-mod-wsgi
    libapache2-mod-wsgi
    

# 文件配置步骤:
1. 参考[样例代码](https://modwsgi.readthedocs.io/en/develop/user-guides/quick-configuration-guide.html)创建一个包含application的文件,叫做sample.py

    #/home/h/sample.py
    def application(environ, start_response):
        status = '200 OK'
        output = b'Hello World!'
        response_headers = [('Content-type', 'text/plain'),
                            ('Content-Length', str(len(output)))]
        start_response(status, response_headers)
        return [output]
   
1. 在为wsgi.conf添加配置:

    # /etc/apache2/mod-available/wsgi.conf
    WSGIScriptAlias /myapp /home/h/sample.py
    <Directory /home/h>
        <Files sample.py>
            Require all granted
        </Files>
    </Directory>

1. 重启apache2后,在浏览器中打开上述配置的url: /myapp , 如果显示 _Hello World!_,说明配置正确.否则检查上述文件,并对照下述文档做修改

    1. django文档
    1. apache文档
    1. mod_wsgi文档
    1. ubuntu文档
 
1. 把sample.py的路径改为你创建的project的wsgi.py

# 大功告成!
呃... 稍等. 可能还是会遇到一些问题. 可以参考一下下面我总结的tips

# 某些可能的错误和tips!
1. 如果有异常,把apache的LogLevle设置为info, 并检查(在ubuntu16.04上)/var/log/apache2下的日志文件.

1. ImportError: No module named django.core.wsg
可能是你用普通用户安装了django这个模块,而不是用系统用户.这将导致django这个模块只能给特定用户使用.  

解决方法: 用root用户安装django. 以及其他项目用到的模块

    sudo apt-get install django

1. RuntimeError: populate() isn't reentrant
很可能是在settings中的app部分配置了多个app. 默认只能运行一个.要想运行多个app的解决方法是: 在 _/etc/apache2/mod-available/wsgi.conf_ 文件中添加类似如下的配置,具体参考[这里](https://modwsgi.readthedocs.io/en/develop/user-guides/quick-configuration-guide.html)

    WSGIDaemonProcess example.com processes=2 threads=15
    WSGIProcessGroup example.com

1. ImportError: No module named mysite.settings
解决方法在[这里](http://stackoverflow.com/questions/36210686/importerror-no-module-named-mysite-settings-django): 

    import sys
    #Wrong! should not add the project settings level, hence where wsgi.py locates
    #sys.path.append("/home/user/mysite/mysite")
    #Correct, should add the project root level
    sys.path.append("/home/user/mysite")

