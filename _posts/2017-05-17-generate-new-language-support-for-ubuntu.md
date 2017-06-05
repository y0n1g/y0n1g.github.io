---
title: 为ubuntu生成新的语言支持
layout: post
category: [tech]
tags: [ubuntu,language]
---
{% include JB/setup %}
## 更新生成项

编辑/etc/locale.gen. 比如把zh_CN.UTF-8 UTF-8之前的注释去掉

## 生成新语言
运行 locale-gen

也可以直接运行 locale-gen zh_CN.UTF-8

