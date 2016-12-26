---
title: django的中间件
layout: post
category: [tech]
tags: [django,python,middleware]
---
{% include JB/setup %}
中间件,就是一种流式处理的架构,也可以称为分层处理. 因此每一层的顺序就很重要了.  
对于django,其中间件系统是插入在输入(request)和输出(response)位置,用钩子实现. 所有的输入/输出,都可以经过安装的中间件处理.  


