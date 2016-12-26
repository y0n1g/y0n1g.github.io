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

# 翻译文字
1. 为某一种语言创建一个信息文件

    python manage.py makemessages -l zh

