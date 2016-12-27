---
title: django的templates
layout: post
category: [tech]
tags: [django]
---
{% include JB/setup %}
django的template可以作为一个库直接使用.  
这个库的用途是,创建模板,并把变化的数据填入模板.  
同时还有一些通用函数支持国际化,可读化等.


# 使用方法
+ 创建Template的对象t
+ 创建Context的对象c
+ 用t渲染c

# Template

    from django import template
    t = template.Template('Hello {{ person }} !')

your_template_string的语法如下:

    > {{ variable }} 
    > {% statement %}

## 关键字
### if/else/endif
+ if语句不允许and or混合使用.
+ 没有else if.可以这么使用

    > {% if %}
    > {% else %}
    >     {% if ... %}
    >     {% endif %}
    > {% endif %}

### for/endfor/empty
+ 可以嵌套使用for
+ 一般在for之前,用if检查是否需要循环. 有个empty可以替代之前的if和最后的endfor
+ 在循环内部,forloop.counter是计数器可以使用;forloop.revcounter是剩余循环数.forloop.last和forloop.first是布尔值,用于第一项和最后一项检测,以便输出的处理.forloop.parentloop用于嵌套循环.

### ifequal/ifnotequal/else
+ 可以硬编码判断是否相等.只能比较字符串,数字
+ 

### 注释
+ 单行注释

    > {# 注释内容 #}

+ 多行注释

    > {% comment %}
    ...
    > {% endcomment %}

### include

    > {% include 'somewhere/template.tpl' %}


### block/endblock/extends
block表明子模板可以重载此部分,endblock结束部分;extends继承.  
extends必须在第一行,否则不工作.



## 过滤器
是变量在显示之前修改显示方法的工具,过滤器用|连接  
一般要先载入相应的模块,才能使用相应的过滤器.  
比如:

    > {% load humanize %}
    > {{ 45000|intcomma }}
    _在载入humanize以后,就可以它定义的intcomma了_



# Context
Context是k-v的字典,用于传递给Template渲染出最终结果.

    from django.template import Context
    c = Context({'person': 'James',})

由于模板可以访问context的函数,为了避免模板错误使用,函数可以明确设置*alters_data = True*这个属性,从而避免模板的调用.


# tips
## render_to_response
可以调用这个函数来直接render结果.不再需要创建Template和Context的对象.


# 问题
## django.core.exceptions.ImproperlyConfigured:

    django.core.exceptions.ImproperlyConfigured: Requested setting TEMPLATES, but settings are not configured. You must either define the environment variable DJANGO_SETTINGS_MODULE or call settings.configure() before accessing settings.

原因在于在django外部试图直接使用template.


