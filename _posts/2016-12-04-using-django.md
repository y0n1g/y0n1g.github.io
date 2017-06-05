---
title: 使用Django
layout: post
category: [tech]
tags: [python, django]
---
{% include JB/setup %}

# 参考源:
+ django官方文档
+ The Django Book

# 一步步使用django做开发
## 项目
django的应用是运行在项目中的.创建项目:

    django-admin startproject mysite
    _在一个项目中可以创建多个app.一个项目只需要运行一次这个命令即可._

+ 此时会创建目录mysite,里面有项目结构所需要的各种文件.    
+ django用到的开发工具在mysite下,名为manage.py  
+ 编辑项目配置文件mysite/mysite/settings.py  

为新创建的项目创建数据库信息

    python manage.py migrate
    _这个操作将把settings.py中安装的app的数据库结构建好,在INSTALLED_APPS_
    _在配置好数据库之前,这里将会默认使用sqlite, 在manage.py的同级目录创建sqlite3_
    _老版本的命令是syncdb_

创建管理员帐户

    python manage.py createsuperuser
    _需要至少8位密码_

## 测试项目

python manage.py runserver your_ip_or_domain:your_access_port
example:
python manage.py runserver 192.168.188.128:8000

第一次可能会看到这样的错误:

    django.core.exceptions.DisallowedHost: Invalid HTTP_HOST header: '192.168.188.128:8000'. You may need to add '192.168.188.128' to ALLOWED_HOSTS.

原因在于django的安全机制. 如错误提示,需要将ip添加到ALLOWED_HOSTS中,位置在mysite/settings.py

## app
web应用是基于项目的,也可以是基于app的.app可以打包重用,更灵活一些.在项目中创建app

    django-admin startapp test_app

在test_app目录下会生成一系列文件:

+ models.py 模式文件,定义了应用中抽象出来的数据对象.基于MVC模式开发的核心数据是model,这部分是客观抽象,每个对象映射到数据库的一个表. django会自动将对象的存取修改等操作映射为恰当的数据库访问. 还有独立的包SQLAlchemy是专门做这个工作的.
+ views.py view,定义业务的操作逻辑和显示的主要对象(可变部分).由于显示的对象都是在models中定义的,因此要import models中的类. 其显示方法,基本可以类比于bottle中,被@route()封装的函数. 对于不变的部分,比如页面框架等,是在templates中定义的.
+ templates文件夹. 这部分用于存放模板文件. 页面怎么漂亮,是在这里实现的.
+ satic文件夹. 用于存放静态文件,比如图片,css等.
+ urls.py 定义了url与视图(view)的映射规则,类似于API的定义. url会调用到views中的显示方法. 至于使用哪个模板,可以在这里传递给views的.
+ admin.py 管理界面视图.这种视图用admin.site.register注册,而不是用urls进行映射.
+ test.py. 测试代码
+ apps.py. 应用的配置文件.这个文件需要手工生成.


*基于django的应用开发,就是实现上述python文件,并且编写一些辅助文件完成需要的功能的过程*

## 应用开发顺序
url, 操作逻辑, 对象等, 都需要一起全盘考虑.

1. url结构.

首先要定义url结构,就是应用的API. 在app目录下创建urls.py. 内容类似:

    from django.conf.urls import url
    from . import views
    app_name = 'your_app_name'
    urlpatterns = [
        url(r'^$', views.index,),
        url(r'^api_1/(\d+)/$', views.api_1,),
    ]

    _注意,这里的正则表达式中有$.而在向project注册这个app的url时,那里没有$_

1. 操作逻辑入口和显示入口(view)

在app目录下有views.py. 需要将上述的在urls中映射的回调函数在这里实现.

    from django.http import HttpResponse
    def index(request):
        return HttpResponse("Hello Django!")

    def index(request, digit):
        return HttpResponse("print %d !"%(digit))


1. 业务操作对象(model)

1. 显示模板等...


可能还需要考虑:

    多国语言支持


1. 配置app到项目中.

    + 配置数据库: 这个操作在project的settings.py中.默认的数据库是SQLite.
    + 配置项目识别app: 在settings.py的INSTALLED_APPS中设置

1. 设计应用的数据库和表.

    即设计app的models.py中的类.

1. 为应用创建数据库:

    python manage.py makemigrations your_app
    _这将准备sql语句_
    python manage.py sqlmigrate your_app 0001
    _这将显示sql语句.其中0001是上一步准备好的,有可能是其他编号._
    python manage.py migrate
    _这将执行上述sql语句_

1. 允许adimn帐户修改数据

    在app的admin.py中,admin.site.register(Class_to_admin)

1. 编写视图文件views.py. 同时可能需要在template下创建辅助的template文件.
1. 将views.py注册到your_app的urls.py中以便用户访问.
1. 还将your_app的视图访问规则,注册到项目中. 在项目的urls.py的urlpatterns字段注册. 而项目是通过settings.py中的ROOT_URLCONF指定了本项目有哪些url是使用的.:w

至此,应用已经开发完毕. 问题在于,各个文件模块,都有一些小技巧需要掌握,这样才能做到事半功倍地快速开发系统. 这需要深入了解django的各个辅助模块,才能充分利用其功能.

## 将app添加到project中

将app添加到project中:

    INSTALLED_APPS in mysite/settings.py

将app的url规则添加到project中:

    urlpatterns in mysite/urls.py

    型如:
    url(r'^your_app_portal/', include('your_app_portal.urls')),
    _注意,上述正则表达式中,没有$.用这种方法, 可以任意将应用放到一个自定义的your_app_portal中,可以随意改变(最好别变),而不会影响app的逻辑._


## 访问时发生了什么
当访问一个定义了view的URL时，Django 根据 ROOT_URLCONF 的设置装载 URLconf 。 然后按顺序逐个匹配URLconf里的URLpatterns，直到找到一个匹配的。当找到这个匹配的URLpatterns就调用相关联的view函数，并把 HttpRequest 对象作为第一个参数。视图函数返回一个HttpResponse. Django转换HttpResponse为一个适合的HTTP response， 以Web page显示出来.


## app中的一些tips
### model.py
+ 注意为其创建unicode处理. 
默认的例子只是使用__str__处理ascii.但是在真实使用中,不应该定义__str__,Model会正确处理.所以,只定义__unicode__即可!

    class Choice(models.Model):
        def __unicode__(self):
            return self.choice_text

+ 为修改数据的函数添加 *alters_data = True*
这样可以避免template系统在显示时错误调用model的函数引发不期望的事情,比如删除/更新等.

数据有一对一,一对多,多对多的情况. 各种情况访问(样例)[http://www.cnblogs.com/pythonxiaohu/p/5814247.html]

+ 对于有外键字段的类,创建时必须指定这个字段的值.调用create()函数创建对象时,必须save().否则如果直接调用类的默认创建函数,则不需要调用save().(?really)

### 数据库的一对多和多对多在django的实现
    参考: http://830909.blog.51cto.com/8311014/1765596

### urls.py
+ Django在检查URL模式前，移除每一个申请的URL开头的斜杠(/)。
+ 任何不匹配或尾部没有斜杠(/)的申请URL，将被重定向至尾部包含斜杠的相同字眼的URL。 （这是受配置文件setting中APPEND_SLASH项控制的
+ 支持静态url和动态url


### template
基本调用模板渲染的方法样例如下:

    >>> from django import template
    >>> t = template.Template('My name is {{ name }}.')
    >>> c = template.Context({'name': 'Adrian'})
    >>> print t.render(c)
    My name is Adrian.
    >>> c = template.Context({'name': 'Fred'})
    >>> print t.render(c)
    My name is Fred.
    
### views中对object的操作
1. 获取单个对象

    Publisher.objects.get(name="Apress")
    _注意,如果有多个匹配或者没有匹配,会抛出异常: MultipleObjectsReturned/DoesNotExist_

1. 返回结果排序

    Publisher.objects.order_by("name")  
    另一种方法是在Model的class中定义class Meta,设置ordering字段.

1. 更新某个字段

    Publisher.objects.all().update(country='USA')

1. 删除对象

    Publisher.objects.get(name="O'Reilly")
    Publisher.objects.filter(country='USA').delete()


### 表单处理
django的表单类在django.forms. 其使用方法类似models,每个字段也是用了Field的形式.比如:

    from django import forms
    class ContactForm(forms.Form):
        subject = forms.CharField()
        email = forms.EmailField(required=False)
        message = forms.CharField()
    

------------------------------------------------------------------------------

# MVC在django的应用
## model

+ 一般,每个model对应一个db的表.
+ 每个model是一个class,继承自django.db.models.Model.
+ model的每个字段(models.Field)对于那个表中的一列
+ 作为持久化层,DB的访问被django提供的API所屏蔽了.直接使用API.



# django特有的
## templates语言

# django的架构说明
## templates
## admin
