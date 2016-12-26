---
title: Django的技巧
layout: post
category: [tech]
tags: [django,python]
---
{% include JB/setup %}
# 耦合
原则是解耦合,各个模块分别开发各自关注的内容.  
由于交互性和互操作的要求,还是有一些耦合性在设计时需要注意

### template和model的耦合
template在显示时要调用model中的各个域,因此这部分是耦合在一起的.  
在设计时,model应该和template一起进行. 设计哪些字段需要显示,叫什么名字等等.
同时, URL的结构(site_map)也应该确定.  
特别是,post的字段有可能在其他url部分,这是硬编码的.

### model和view的耦合


### view,url和template的耦合
进行HttpResponseRedirect时,路径是硬编码的.导致view中的内容和url不能分开.
# 其他包:
+ 图片上传. 如果要使用上传图片的功能(Field定义在Model和Form), 则需要Pillow包.
+ 模板. Django还支持第三方template系统. 比如jinja.

# 其他
+ 安装django的包
在INSTALLED_APPS添加了django的包以后,要注意该包是否需要在MIDDLEWARE部分添加内容.  
对于django的包,不需要做makemigrations操作, 而是直接做migrate来创建数据库的表.

    python mange.py migrate

+ template搜索顺序
django会在project级别的templates目录下搜索, 然后再在各个app下的templates目录下. 因此,如果有同名子目录和文件,会优先在project级别命中,而不是在app级别命中.

+ middleware的执行顺序
django的setting中,middleware是一个多元组. 对于response和异常处理: 执行顺序是**从后(下)至前(上)**. 因此,如果需要某个middleware稍后执行,则在setting中需要放在相应考前(上)的位置.  对于request和view阶段:执行顺序是** 从前(上)至后(下)**!

