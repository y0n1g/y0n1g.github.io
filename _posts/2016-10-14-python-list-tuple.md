---
layout: post
title: python的列表和元组
category: python
---
{% include JB/setup %}
#种类
一种是长度可变列表[]
一种是长度不可变,内容不可修改的列表(),称为元组

#切片
a[0:]
a[3:8]
b(:-1)
b(7)

#常用函数
for跟perl的用法基本一致.

range(),一般可以跟len()函数配合使用.
比如range(len(a_string)), 将产生一个列表,里面是从0到字符串长度值的数组,比如[0,1,2,3,4,5,...18]

#常用解析方法

    squared = [x ** 2 for x in range(4)]
    sqdEvens = [x ** 2 for x in range(8) if not x % 2]


