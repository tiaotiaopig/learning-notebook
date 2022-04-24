# Git 学习心得

> Git 命令学习网站:[git学习](https://learngitbranching.js.org/?locale=zh_CN)

## 基本概念

`git init` 之后会在当前目录下,创建一个`.git`的隐藏目录,保存了仓库的所有信息

总共有四块区域:工作区,暂存区,历史提交区,远程仓库

> 1. 工作区(**working dir**)
>
>    就是当前的工作目录
>
> 2. 暂存区(**stage/index area**)
>
>    在工作区修改文件后,使用**add**命令,将修改暂存到这里
>
> 3. 提交历史(**commit history**)
>
>    使用commit命令将暂时区的修改进行提交,会生成提交历史树的一个新节点,一般使用一个40位的哈希值进行标识(在**checkout**时完整的哈希值不是必须的,给出部分即可)
>
> 4. 远程仓库(**remote repository**)
>
>    将本地的提交历史保存到远程服务器,从而大家可以共享

## 分支

每一次提交都会形成一个节点,提交历史(仓库)就是一棵树(严格说是图)

使用`branch`命令创建,或者`git checkout -b branch-name`创建分支并切换

`git branch -f branch-name hash`强行修改分支指向

`tag`可以理解为里程碑,固定指针

分支很好理解,就是搞个分叉嘛,每个分支有个名字,其实就是个节点指针

`HEAD`是一个特殊的指针,代表当前所处的位置,可以指向节点,也可以指向分支

使用**checkout**命令可以改变`HEAD`指向

### merge 和 rebase

> 分支合并的两种方式:
>
> `git merge branch-name`创建一个新的提交节点,将当前分支和指定分支合并,并将HEAD指向这个新节点
>
> `git rebase branch1 branch2`将分支2的提交合并分支1后面(分支1在前,分支2在后),**HEAD**指向分支2
>
> `git rebase branch1 [HEAD]`只有一个参数时,相当于和HEAD做合并

## fetch pull push

> `git fetch`拉取所有远程分支,不做合并
>
> `git pull = git fetch + git rebase origin/main`
>
> `git push`当前分支推送,如果当前分支没有追踪远程分支的话会报错
>
> `git push origin main` 会将本地追踪远程分支**origin main**的本地分支推送到远程
>
> `git push origin local:main`将本地分支推送到远程main上,不存在则新建
>
> 说明:追踪关系git有个默认,一般就是名字相同
>
> 也可以手动设定,不过没必要
>
> 一种直接从远程分支checkout,另一种使用**-u**参数指定
>
> `git checkout -b foo origin/main`
>
> `git branch -u origin/main foo`

git 命令太灵活了,同样的功能可以使用不同的命令实现,我们需要记住常用的几个即可