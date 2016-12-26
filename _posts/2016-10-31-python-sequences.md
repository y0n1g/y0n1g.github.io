---
title: python sequences
category: [python]
layout: post
---
{% include JB/setup %}
# Sequences
python的sequence包含两种, 一种是immutable(其值无法改变); 一种是mutable.

## immutable
包含三种,在创建后其值不可改变. 注意,如果其值是引用,则引用指向的对象可能可以改变,但是引用本身(指针)不能改变.

1. Strings  字符串

1. Unicode

1. Tuples 元组, 用()定义

## mutable
有两种:

1. List

1. Byte Array


# 类 Class
命名空间搜索顺序:
For old-style classes, the search is depth-first, left-to-right in the order of occurrence in the base class list. New-style classes use the more complex C3 method resolution order which behaves correctly even in the presence of ‘diamond’ inheritance structures where there are multiple inheritance paths leading back to a common ancestor. 
