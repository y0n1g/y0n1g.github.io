---
title: Django的表格form
layout: post
category: [tech]
tags: [django, python]
---
{% include JB/setup %}
Form的架构与Model很类似.  
Form有bound和unbound两种状态.bound时,可以验证数据,可以render;unbound时,只能做render.  
admin的管理界面,貌似就是将Model转换成了Form来使用.

# 函数
+ clean(). 验证字段,可能抛出异常.
+ has_changed(). 检测字段是否不是默认值.

# Field的核心字段

+ required. 默认True.设置为False时,clean()可以通过空值检测.
+ lable. 设置为字符串.会显示在字段之前作为说明文字
+ lable_suffix.
+ initial. 设置Field的初始值.如果要动态设置,参考Form.initial
+ widget. 配置widget.
+ help_text.
+ error_messages. 设置为字符串,将会覆盖clean()函数抛出异常的文字.
+ validators. 验证器
+ localize. 
+ disabled. 禁止用户修改.

# Field的内建类.
+ BooleanField.
+ CharField. 包含max_length和min_length两个字段.
+ ChoiceField. 包含choices,同样的choices也用于Model的字段.
+ TypedChoiceField. 跟Choicefield类似,多了两个字段:

    coerce - 强制转换函数.
    empty_value - 表示空值的内容.

+ DateField.
+ DateTimeField.
+ DecimalField. 包含如下字段: max_value/min_value/max_digits/max_decimals
+ DurationField. Expects data in the format "DD HH:MM:SS.uuuuuu" or as specified by ISO 8601
+ EmailField.
+ FildField.  包含字段:max_length/allow_empty_file.
+ FilePathField. 支持在某些目录选择文件. 包含字段:

    path - 希望list的目录,目录必须存在.
    recursive - 是否显示子目录的内容.默认False.
    match - reg模式,只有满足reg的文件名会显示.
    allow_files(True)/allow_folders(False). 两者必须有一个设为True.

+ FloatField. max_value/min_value
+ ImageField. 这个字段需要Pillow包支持.
+ IntegerField. max_value/min_value
+ GenericIPAddressField. protocol字段可以限制支持的协议(ipv4/ipv6/both)
+ MultipleChoiceField. 需要一个额外的字段choices.
+ TypedMultipleChoiceField. 类似MultipleChoiceField, 只是多了两个字段: coerce和empty_value
+ NullBooleanField.
+ RegexField. 字段: regex字符串, strip是否允许字段
+ SlugField. 字段allow_unicode默认False. slug是一个报纸术语,只能包含字母,数字,下划线和-,常用在url中.
+ TimeField.
+ URLField. 包含max_length/min_length
+ UUIDField.

# 复杂内建类
+ ComboField. 通过fields字段可以支持多种验证.
+ MultiValueField. 可以支持多种值的抽象类.必须实现compress()函数,不能实现clean()函数.
+ SplitDateTimeField

