---
title: Django的url
layout: post
category: [tech]
tags: [django,python]
---
{% include JB/setup %}
django的url与view,是通过urls.py这个配置文件实现的,具有很强的灵活性.
样例是基于djang的标准文档

# 前提
django已经配置完成,可以提供web服务了.假设项目名为st,在apache中的映射为127.0.0.1/st

# 项目级别的urls

项目级别的urls.py, 形如如下:

    from django.conf.urls import url
    from django.contrib import admin

    #Added manually
    from django.conf.urls import include

    urlpatterns = [
        url(r'^polls/', include('polls.urls')),
        url(r'^admin/', include(admin.site.urls)),
    ]

经过上述配置后,对于team这个app,可以通过url127.0.0.1/st/team访问.


# app级别的urls


    from django.conf.urls import url

    from . import views

    urlpatterns = [
        # 所有127.0.0.1/st/polls 的访问, 都会在这里进行查找映射.
        #
        # ex: /polls/
        # 不传递任何参数
        url(r'^$', views.index, name='index'),

        # ex: /polls/5/
        # 函数声明变量名,用正则表达式传入
        url(r'^(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
        # 会这样调用views.detail
        # detail(request, question_id=5)

        # ex: /polls/5/results/
        url(r'^(?P<question_id>[0-9]+)/results/$', views.results, name='results'),
        # ex: /polls/5/vote/
        url(r'^(?P<question_id>[0-9]+)/vote/$', views.vote, name='vote'),

        # 函数不声明变量名, 以位置参数传入
        # ex: /polls/articles/2003
        url(r'^articles/2003/$', views.special_case_2003),

        # ex: /polls/articles/2012
        url(r'^articles/([0-9]{4})/$', views.year_archive),
        # 会这样调用views.year_archive
        # year_archive(request, '2012')

        # ex: /polls/articles/2012/11
        url(r'^articles/([0-9]{4})/([0-9]{2})/$', views.month_archive),
        # 会这样调用views.month_archive
        # month_archive(request, '2012', '11')

        url(r'^articles/([0-9]{4})/([0-9]{2})/([0-9]+)/$', views.article_detail),
    ]







