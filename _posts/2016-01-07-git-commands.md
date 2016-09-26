---
layout: post
title: git使用
category: tech
tags: [git,github]
---
{% include JB/setup %}

[TOC]


# 最佳实践
- 及时本地提交commit
- 减少push的量
- 切换分支时，要把本地的修改先提交**或隐藏(stash)**以保证本地干净。否则git会因为冲突阻止切换。


#常用命令

大部分内容来自于： http://git-scm.com/book/en/v2

- 将一个已有的远程git库克隆到本地进行学习/修改/更新等操作：
> git clone https://github.com/HU-YN/hu-yn.github.io.git
> 注意，这里的 . 表示将内容克隆到当前目录。 也可以用一个新的目录名代替。clone是对远程仓库的一个完整复制。即使服务器出现了故障丢失了一切，都可以从本地这个仓库完全恢复。
> 会自动将其添加为远程仓库并默认以 “origin” 为简写

- 同步服务器的新代码。如果之前做过clone，需要更新本地文件反映服务器端最新变化，则：
> git fetch origin

- 添加一个文件，或标注一个修改了的文件
> git add newOrchange_file_to_the_repository
> 注意，这个操作只是做了一个标识，告诉git打算添加一个新文件，或者某个文件需要commit.

- 撤销待commit的文件，返回当前分支的HEAD版本
> git reset HEAD newOrchange_file_to_the_repository
> 这个命令将使下一次commit不再添加本次文件的修改。注意，文件的修改本身是保留在文件中的。特别是在运行了 git add *以后，所有文件都会被更新。 这时发现有几个文件不应该在这一次提交更新，就运行上述命令。

- 返回某个文件的之前的版本(而不是HEAD版本)
> git log
> 首先获取变更的sha列表
> git reset 7ea1dfcb0db860  file_to_revert
> file_to_revert就会返回某个之前的版本。注意！本地对file_to_revert的修改依然保留！所谓返回之前的版本只是版本号进行了返回，而修改未变。这时运行git status可以看到当前的修改与之前某个版本之间的差异。
>  **危险** git reset --hard HEAD~  file_to_revert
> 这个版本的命令将会把本地修改丢弃！！强制完全回滚到HEAD版本！危险！
> 其实 git checkout branch可能更安全一些。也是直接切换到某branch的HEAD版本，但是有冲突检测操作

- 分支返回到某个版本
> 前面的reset命令，如果不指定某个/些文件，则将作用在当前的整个分支上

- [checkout和reset的差异](http://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E7%BD%AE%E6%8F%AD%E5%AF%8)
> ![差异](http://git-scm.com/book/en/v2/book/07-git-tools/images/reset-checkout.png)


- [压缩提交](http://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E7%BD%AE%E6%8F%AD%E5%AF%86)
> 在本地做了多次commit后
> git reset --soft 7ea1dfcb0db86
> 将head指针指向之前的版本。
> git add *
> 再把所有变更一次性添加
> git commit -m 'compressed a lot of commits...'
> 这将把没有push之前的从指定版本开始的所有commit合并成1个commit提交
> git push
> 最后把变更提交给服务器。


- 撤销修改
> git checkout -- file_to_recall_modification
> 上述命令将**把本地的修改丢弃**，转而到上一个版本的文件。 注意，这个命令比较危险！务必确认修改真的需要


- 查看本地仓库做了哪些修改？
>git status
>注意，只是以文件基本现实变化

- 查看具体的差异
>git diff
>注意，此命令比较的是工作目录中当前文件和暂存区域快照之间的差异，也就是修改之后还没有暂存起来的变化内容。
>如果要查看已经暂存起来的文件和上次提交时的快照之间的差异，可以用 git diff --cached 命令。（Git 1.6.1 及更高版本还允许使用 git diff --staged，效果是相同的，但更好记些。）

- 提交修改(到本地)
> git commit
> 也可以直接添加comment，而不打开编辑器：
> git commit -m 'the change comment for this commit...'

- 修正已经提交的commit
> git  commit --amend
> 当commit完成后，发现有其他文件没有提交（现在运行git add把需要添加的文件夹上）， 或者发现注释写的有问题，则运行 commit --amend。 这将更新前一次的提交，也有机会重写注释。

- 查看当前分支的提交历史记录
> git log


- 创建分支(注意，这个分支是创建在本地的！跟远程的服务器上的分支没有关系！)
> git branch test_branch

- 切换分支
> git checkout test_branch
> 注意：切换分支时如果有冲突会导致切换失败。

- 切换分支遇到冲突时
> git stash 或 git stash save
> 可以在当前分支缓存当前修改，而不需要提交（比如修改并没有完成）
> git stash list
> 可以查看所有缓存修改
> git stash apply
> 应用缓存。注意，可以应用其他分支的缓存！
> git stash drop stash@{0}
> 可以丢弃某个缓存。注意，在缓存所在分支， 如果经过了分支切换后返回所在分支，则缓存的修改病没有应用到所在分支！ 应该先apply后，再丢弃缓存！



- 创建和切换两个操作可以一次实现：
> git checkout -b test_branch
> 注意，这个操作将会在**当前**所在的文件集合（分支）上创建新的分支。

- 将test_branch合并到master分支
> git checkout master
> 先切换到master分支
> git merge test_branch
> 再做合并。合并可能会有冲突。
> git merge --abort
> 如果不想进行merge，则用上述命令可以把自动merge的内容清除。
> git status
> 上述命令查看哪些文件有合并的冲突。 并且用编辑器打开相应文件进行手工修正。
> git commit
> 手工做了修正以后，最后做commit操作提交改动。

- 分支使用完毕后，删除分支
> git branch -d test_branch

- 查看当前仓库的分支情况
> git branch

- 将本地的改动提交到服务器
> git push
> 注意，需要设置一些变量。具体可以参考push.default说明。


- 显示当前分支的提交历史
> git log

- 显示某一次提交的具体变化：
> git show 734713bc047d87bf7eac967
> 其中，后面的id是在git log中显示的id列表的前缀。 可以在没有冲突和歧义的情况下使用较短的前缀。

- 打标签
> git log
> 先显示之前的提交历史
> git  tag -a v1.3  9fceb023uivg
> 为9fceb023uivg这次改动添加版本标签v1.3
> git tag -a v1.4 9fceb023uivg -m 'my version 1.4'
> 为9fceb023uivg这次改动添加tag并且添加注释
> git show v1.4
> 显示v1.4这个tag的详细信息
> git tag
> 显示所有的tag
> git push origin v1.4
> 注意，push命令不会推送tag。因此必须显式地推送tag到某个分支
> git push origin --tags
> 可以把本地所有的tag都推送到服务器。适用于批量推送tag的情形
> git checkout branch_name tag_name
> 用上述命令获取某个版本（tag）标识的文件。


- 显示本地git的配置：
> git config --list

- [配置常用别名](http://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-Git-%E5%88%AB%E5%90%8D)
> git config --global alias.co checkout
> git config --global alias.br branch
> git config --global alias.ci commit
> git config --global alias.st status
> git config --global alias.unstage 'reset HEAD --'
> git config --global alias.last 'log -1 HEAD'

- [在windows上配置系统记住用户名和密码](https://help.github.com/articles/caching-your-github-password-in-git/)
> git config --global credential.helper wincred

- 为一个仓库添加不同的分支和其他人开发的库，这里别名为tb
> git remote add tb https://github.com/HU-YN/test.git
> 注意！git本身不会区别同一个仓库添加的各个分支是否来自同一个项目！ 因此如果在同一个仓库中添加了不同的项目，将会导致大混乱！
> 格式为：  git remote add [shortname] [url]
> 下一步可以获取添加的某个库的内容
> git fetch tb


# 注意事项
- fetch只获取代码，而pull会进行merge操作。



# 示例1 - **凭空**创建
-  首先在github上创建好一个新的测试仓库， https://github.com/HU-YN/test.git

- 在本地，初始化空的仓库
> git init
> git add README.md
> git commit -m "first commit"
> git remote add origin https://github.com/HU-YN/git.git
> git push -u origin master
> 上面的命令也可以分成两步。 第一步是设置up-stream， 第二步是push
> 注意，本例只适合于空的仓库。如果仓库已有一些文档，则在push时会发生冲突，需要本地的master与远程的master分支进行合并。合并的命令是(以master有冲突为例)：
> git pull origin master
> 上述命令将把远程origin代表的仓库的master分支合并到本地

# 示例2 - clone已有项目
- 以上面的test.git为例
- 直接clone项目到当前目录 
> git clone https://github.com/HU-YN/git.git .
> git checkout branch1
> 注意，前面clone结束后，调用branch -v，只能显示master分支。 但其实该项目所有其他分支也都在本地。 因此调用checkout可以直接切换。

# 示例3 - 从空的库获取远程的代码
- 以上面的test.git为例。 注意，本示例是示例2的人工形式。用示例2就挺好的。
> git init
> git remote add origin https://github.com/HU-YN/git.git
> git pull origin
> 注意，上述也可以运行git fetch origin，但是fetch不进行merge操作
> git checkout master