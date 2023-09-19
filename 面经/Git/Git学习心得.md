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
>    撤销操作:`git rm --cached [filename]`或者 `git reset HEAD [filename]`
>
>    如果想丢弃工作区修改,恢复到上一次commit时的状态,`git checkout -- filename`(慎用)
>
> 3. 提交历史(**commit history**)
>
>    使用commit命令将暂时区的修改进行提交,会生成提交历史树的一个新节点,一般使用一个40位的哈希值进行标识(在**checkout**时完整的哈希值不是必须的,给出部分即可)
>
>    并不需要撤销,修改即可 `git commit --amend`可以将暂存区文件提交,可以修改提交信息,其实所谓的修改,本质上使用一个新的提交代替原来旧的提交
>
>    `git tag -a v1.0 checksum -m '标签说明'`
>
>    `git show v1.0`
>
>    -a 附注标签,
>
>    给指定提交打标签,并附上说明
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
>
> 总的原则是，只对尚未推送或分享给别人的本地修改执行变基操作清理历史， 从不对已推送至别处的提交执行变基操作

## cherry-pick

> 非常灵活的命令,很好用
>
> `git cherry-pick c1 c5 c7`依次选取c1 c5 c7三次提交,加到HEAD后,并移动到最新

## remote

> ```shell
> # 查看所有远程仓库名称和url
> git remote -v
> # 查看详情
> git remote show [remote-name]
> git remote rename old new
> ```

## fetch pull push

> `git fetch`拉取所有远程分支,不做合并
>
> `git pull = git fetch + git merge origin/main`
>
> `git pull --rebase`
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

## .gitignore

> 1. 修改.gitignore文件后，使其生效
>
>    `git rm -r --cached .
>    git add .
>    git commit -m "Fixed untracked files"`
>
> 2. 常见写法
>
>    > 1. `foo`: 匹配所有路径中名为`foo`文件和目录
>    > 2. `*.log`：匹配所有.log文件
>    > 3. `build/`：匹配`build/`目录及其所有子目录内容
>    > 4. `build/`*：匹配`build/`目录及其内容，但不包括其子目录
>    > 5. `**/config/*.cfg`：匹配所有 `config` 目录及其子目录中的 `.cfg` 文件
>
>    注意：`foo`和`/foo`区别：前者匹配所有目录和文件，后者匹配根目录下的