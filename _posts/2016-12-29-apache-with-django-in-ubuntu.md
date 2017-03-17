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

1. Exception Value: 'WSGIRequest' object has no attribute 'user'
很诡异的事情是,用django自带的web服务器,运行admin这个页面完全没有问题.切换到apache后就不能使用了,提示上述错误. 在StackOverflow上找到了下面这个修改可以解决问题. 同时产生的一个问题是admin的UI异常.

    把settings中的MIDDLEWARE改成MIDDLEWARE_CLASSES!
    修改后原始django自带的web服务器依然可以运行admin页面.apache也可以运行admin页面.只是显示不好看,原因在于apache已经配置了static目录,而django的admin模板使用了本地的css/js文件,导致apache无法提供这些文件.解决方法有:
    1. 将django自带的css/js文件拷贝到apache的静态文件目录
    2. 将django自带的文件地址配置到apache

经过检查,django在1.10时做了修改,这样会导致版本不一致时变量名有差异. 在我的机器上导致上述错误的原因是root在全局安装的django版本与我自己安装的django版本不一样!  
所以根本的解决方法是,使用一致的django版本.



