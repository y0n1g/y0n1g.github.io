---
title: Django的http相关的内容
layout: post
category: [tech]
tags: [django,http]
---
{% include JB/setup %}
# url
url需要精巧地设计.WWW的创建者Tim Berners-Lee认为[好的URI不应该有变化](http://www.w3.org/Provider/Style/URI). 这在当前REST潮流下显得更加重要.  
配置了URL结构后,URL中正则表达式匹配的内容会以位置参数(或命名参数,如果给正则表达式起了名字的话)传递给view的函数.  
样例:

    from django.conf.urls import url
    from . import views
    urlpatterns = [
        url(r'^articles/2003/$', views.special_case_2003),
        url(r'^articles/([0-9]{4})/$', views.year_archive),
        url(r'^articles/([0-9]{4})/([0-9]{2})/$', views.month_archive),
        url(r'^articles/([0-9]{4})/([0-9]{2})/([0-9]+)/$', views.article_detail),
        url(r'^articles/2003/$', views.special_case_2003),
        url(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
        url(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive),
        url(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/(?P<day>[0-9]{2})/$', views.article_detail),
        
    ]
    
切割复杂度

    from django.conf.urls import include, url
    from apps.main import views as main_views
    from credit import views as credit_views
    extra_patterns = [
        url(r'^reports/$', credit_views.report),
        url(r'^reports/(?P<id>[0-9]+)/$', credit_views.report),
        url(r'^charge/$', credit_views.charge),
    ]
    urlpatterns = [
        url(r'^$', main_views.homepage),
        url(r'^help/', include('apps.help.urls')),
        url(r'^credit/', include(extra_patterns)),
    ]
    
优化

    from django.conf.urls import url
    from . import views
    urlpatterns = [
        url(r'^(?P<page_slug>[\w-]+)-(?P<page_id>\w+)/history/$', views.history),
        url(r'^(?P<page_slug>[\w-]+)-(?P<page_id>\w+)/edit/$', views.edit),
        url(r'^(?P<page_slug>[\w-]+)-(?P<page_id>\w+)/discuss/$', views.discuss),
        url(r'^(?P<page_slug>[\w-]+)-(?P<page_id>\w+)/permissions/$', views.permissions),
    ]
        
    from django.conf.urls import include, url
    from . import views
    urlpatterns = [
        url(r'^(?P<page_slug>[\w-]+)-(?P<page_id>\w+)/', include([
            url(r'^history/$', views.history),
            url(r'^edit/$', views.edit),
            url(r'^discuss/$', views.discuss),
            url(r'^permissions/$', views.permissions),
        ])),
    ]
   
included的URL会接收到parent的URL所有捕获的参数:

    # In settings/urls/main.py
    from django.conf.urls import include, url
    urlpatterns = [
        url(r'^(?P<username>\w+)/blog/', include('foo.urls.blog')),
    ]

    # In foo/urls/blog.py
    from django.conf.urls import url
    from . import views
    urlpatterns = [
        url(r'^$', views.blog.index),
        url(r'^archive/$', views.blog.archive),
    ]
    
可以在url这里用灵活的方式给view传递额外的参数:

    from django.conf.urls import url
    from . import views
    urlpatterns = [
        url(r'^blog/(?P<year>[0-9]{4})/$', views.year_archive, {'foo': 'bar'}),
    ]
     
