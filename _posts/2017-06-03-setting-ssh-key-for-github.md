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
[github官方文档](https://help.github.com/articles/connecting-to-github-with-ssh/)官方文档已经对key的生成有了比较详细的说明。包含了[测试key](https://help.github.com/articles/testing-your-ssh-connection/)等.

## 前置步骤

1. 将key添加到ssh-agent

github [这篇文章](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#platform-linux)解释得很好.为了让每个shell都可以拥有验证功能,可以参考[这篇文章](https://help.github.com/articles/working-with-ssh-key-passphrases/)在.profile中添加相应的环境设置脚本.

1. 22被屏蔽. 这时可以使用一般不被屏蔽的443(HTTPS)端口.

参考[这里](https://help.github.com/articles/using-ssh-over-the-https-port/)

## 测试方法:

1. 测试方法

    ssh -Tv git@github.com
    ssh -Tv -p 443 git@github.com #如果配置了https

1. 成功返回结果:

    Hi ****! You've successfully authenticated, but GitHub does not provide shell access.

1. 错误结果:



## 为什么在测试成功以后还是需要输入用户名和密码呢?  可能的原因有如下一些:

1. 用ssh/https协议clone到本地.

    出错原因是clone时使用了http协议. 换用ssh协议clone到本地后,push时就不必再输入认证信息了.

