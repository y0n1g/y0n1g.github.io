---
title: vim配置
category : [tech]
tags : [vim]
layout: post
---
{% include JB/setup %}

vim的学习曲线又长又陡。前前后后学了很久。下面把一些经验总结起来供参考：

## 配置文件
vim的配置文件可以自定义很多功能。其中我喜欢并且常见的一些设置是：

- 将F5映射为xml的自动格式化命令：
```
    map <F5> : %!xmllint --format --recover -<CR>
- 需要设置系统环境变量XMLLINT_INDENT，xmllint将使用这个变量决定缩进。比如：XMLLINT_INDENT="    "
```
    
- 自动识别文档类型。这将影响语法高亮等

>command | detection | plugin | indent
>:------------|:---------:|:---------:|:---------:
>filetype on | on | unchanged | unchanged
>filetype off | off | unchanged | unchanged
>filetype plugin on | on |  on | unchanged
>filetype plugin off | unchanged | off | unchanged
>filetype indent on | on |  unchanged | on
>filetype indent off | unchanged | unchanged | off
>filetype plugin indent on | on | on | on
>filetype plugin indent off | unchanged | off | off


## 最好的[命令映射表](http://michael.peopleofhonoronly.com/vim/)：
![vim_cheat_sheet_for_programmers_print](/assets/vim_cheat_sheet_for_programmers_print.png)

这里还有[比较全面的一些映射表](https://rumorscity.com/2014/08/16/5-best-vim-cheat-sheet/)
