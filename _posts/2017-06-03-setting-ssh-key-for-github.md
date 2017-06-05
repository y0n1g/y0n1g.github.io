---
title: 为github设置ssh的key
layout: post
category: [tech]
tags: [ssh,github]
---
{% include JB/setup %}
# 目标
希望配置了key以后，每次push时不必再输入用户名和密码。

# 步骤
(github官方文档)［https://help.github.com/articles/connecting-to-github-with-ssh/］官方文档已经对key的生成有了比较详细的说明。包含了(测试key)[https://help.github.com/articles/testing-your-ssh-connection/]等.

为什么在测试成功以后还是需要输入用户名和密码呢?

可能的原因有如下一些:

1. 用https协议clone到本地.

    换用ssh协议clone到本地后,push时就不必再输入认证信息了.

