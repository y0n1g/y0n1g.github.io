---
title: django的本地化
layout: post
category: [tech]
tags: [django,python]
---
{% include JB/setup %}
在python代码和template中,通过添加一些字符,可以指示django在翻译语言可用时使用特定的语言显示. 为了正常工作,需要GNU的gettext包. 

# 指定待翻译文字
## 在code中
有两种方法指定待翻译文字:
1. _

    from django.utils.translation import ugettext as _
    def my_view(request):
        output = _("Welcome to my site.")
        return HttpResponse(output)
   
1. ugettext

    from django.utils.translation import ugettext
    def my_view(request):
        output = ugettext("Welcome to my site.")
        return HttpResponse(output)

总之,只要在最终输出的内容之前对指定文字/变量运行_或者ugettext即可.  
由于i18n的需求,对于一些日期之类的文字可以用占位符的方法实现指示:

    def my_view(request, m, d):
        output = _('Today is %(month)s %(day)s.') % {'month': m, 'day': d}
        return HttpResponse(output)

注意,对于出现在任何地方的任意文本,只要希望最终显示翻译,就可以用上述方法标记.
## 在template模板中

    {% load i18n %} 
    <title>{% trans "This is the title." %}</title>
    <title>{% trans myvar %}</title>
    <title>{% trans "myvar" noop %}</title>
    {% blocktrans %}This string will have {{ value }} inside.{% endblocktrans %}
    {% blocktrans with value|filter as myvar %}
    This will have {{ myvar }} inside.
    {% endblocktrans %}

# 配置django
只有一步,需要告知django把翻译文件放在哪里(在哪里寻找). 默认的settigns文件没有这个项(结构类似template的配置):

    LOCALE_PATHS=(
        os.path.join(BASE_DIR, 'locale'),
    )
    

# 翻译文字
1. 为某一种语言创建一个信息文件

    python manage.py makemessages -l zh
    _结果会创建locale以及相应的zh目录和文件._
    __注意,为了给某个app创建翻译,可以在app目录下运行如下命令:_
    django-admin makemessages -l zh


# tips
注意,views中访问的model的域,可能不是数据库中的定义或者最终的显示.  
比如在admin的界面中,list_display的字段并不是用于显示的,添加翻译指令不管用.
在正确的位置,添加指示,是关键... 哪里是正确位子呢?