---
layout: post
title: 使用jekyll和github的一些细节
category: tech
tags: [jekyll,github]
---
{% include JB/setup %}
这里列出一些在github上使用jekyll作为blog server的小技巧或者曾经遇到的问题。

> - 目录： 仔细参考[jekyll的目录结构](http://jekyllrb.com/docs/structure/)，不能随意修改名字。我之前不慎将_posts写成了_post，导致jekyll无法发现需要转换的内容。
> - 编码： 如果在windows上使用编辑器编写文档，要使用UTF-8无BOM的格式。否则很可能导致jekyll无法正确转换。
