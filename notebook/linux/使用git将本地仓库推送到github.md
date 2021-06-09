#  使用git将本地仓库推送到github

## git 简单入门

~~~shell
# 初始化 git 本地仓库,会多出 .git 的隐藏文件夹
cd /usr/workspace
git init
# 修改 添加文件后,保存到暂存区
git add --all
# 将修改提交到本地仓库
git commit -m '本次提交的相关信息'
# 将修改提交到远程仓库
git push origin master/main
# 查看当前仓库的状态
git status

# 从远程仓库克隆项目
git clone https://www.github.com/username/projectname
# 每次提交前都应该先从远程仓库更新最新的修改
git pull origin main
~~~

## github上创建远程仓库

> 1. 登录后在用户的主界面,右上角点击加号 -> New Repository -> 设置仓库名称,是否私有,其他的就不要点了
>
> 2. 设置ssh秘钥,右上角点击用户头像 -> setting -> SSH and GPG keys
>
> 3. ```shell
>     ssh-keygen -t rsa -C "youremail@example.com"
>     注意：这个邮箱地址写你自己的
      <<<<<<< HEAD
>     ```
>
> ```shell
>sudo apt-get install xclip
> 
>     # Copies the contents of the id_rsa.pub file to your clipboard
> xclip -selection clipboard < ~/.ssh/id_rsa.pub
> ```
> 
>Downloads and installs xclip. If you don't have `apt-get`, you might need to use another installer (like `yum`)

> ```sh
> sudo apt-get install xclip
> ```

Copies the contents of the id_rsa.pub file to your clipboard

>     xclip -selection clipboard < ~/.ssh/id_rsa.pub
> ```
> 
>     >>>>>> 4ce5956a8f16e483e7f7fdfd76e2afa67da1532e
>4. 接着你会看到一个叫.ssh的目录，里面有两个文件， id_rsa 和 id_rsa.pub，秘钥就在id_rsa.pub文件里，打开它，然后复制 ，粘贴到github的哪个秘钥大框框里，标题随便写，最后点击Add ssh key即可。
> 
> 注:这里查看私钥的密码可以不设置(lifeng我设置的密码)
>     ```

## 使用git将本地仓库推送到远程仓库

```shell
git add hello.py  意思是把hello这个py文件暂存到git仓库里

git add --all    意思是把所有修改的文件暂存到git仓库里

git commit -m "add some file"  意思是把暂存的文件真正地提交到git仓库里

git remote add origin ssh的链接配置    这个就是连接github仓库的，一次连接，这段时间就不用再连接了

git push -u origin master    把本地库的所有已经提交内容推送到远程库上
```

tips:

> 1. `git remote add origin ssh的链接配置` 将本地仓库关联到远程的仓库,可以使用ssh或者http
> 2. `git push -u origin master `加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。
> 3. `git push --delete origin oldName`删除远程分支,github上相应的分支就会删除,但是仍然关联着
> 4. `git remote rm origin`将远程关联解除
> 5. .gitignore文件,在**Git工作区**的**根目录下**创建一个特殊的**.gitignore文件**，然后把**要忽略的文件名填进去，Git就会自动忽略这些文件。**
> 6. .gitignore文件最后也要提交上去.

## git 撤销命令

1. git commit 之后,如何撤销 commit 操作

    修改了本地的代码，然后使用：

    > git add file
    >  git commit -m '修改原因'

    执行commit后，还没执行push时，想要撤销这次的commit，该怎么办？

    解决方案：
     使用命令：

    > git reset --soft HEAD^

    这样就成功撤销了commit，如果想要连着add也撤销的话，--soft改为--hard（删除工作空间的改动代码）。

    命令详解：

    > HEAD^  表示上一个版本，即上一次的commit，也可以写成HEAD~1
    >  如果进行两次的commit，想要都撤回，可以使用HEAD~2

    > --soft
    >  不删除工作空间的改动代码 ，撤销commit，不撤销git add file

    > --hard
    >  删除工作空间的改动代码，撤销commit且撤销add

    另外一点，如果commit注释写错了，先要改一下注释，有其他方法也能实现，如：

    > git commit --amend
    >  这时候会进入vim编辑器，修改完成你要的注释后保存即可。

## 更改远端仓库URL

1. 从 ssh 转换到 https

```shell
# 进入工作目录
cd workspace
# 查看远程仓库信息
git remote -v
# 更改为https链接
git remote set-url origin https://github.com/USERNAME/REPOSITORY.git
# 也可以先删除ssh,再加https
git remote rm origin
git remote add origin https://
```

2. 从 https 到 ssh

   ```shell
   # 进入工作目录
   cd workspace
   # 查看远程仓库信息
   git remote -v
   # 更改为https链接
   git remote set-url origin git@github.com:USERNAME/REPOSITORY.git
   ```

3. 使用插件提供的加速https链接,并将密码缓存

    ~~~bash
    # 默认是保存15分钟
    git config --global credential.helper cache
    # 长久存储,执行该命令后,再pull或者push下,输入用户名和密码,下次就不用输入了
    git config --global credential.helper store
    # 增加远程地址的时候带上密码也是可以的。(推荐)
    http://yourname:password@git.oschina.net/name/project.git
    ~~~


## https方式保存密码

https方式每次都要输入密码，按照如下设置即可输入一次就不用再手输入密码的困扰而且又享受https带来的极速

设置记住密码（默认15分钟）：

```
git config --global credential.helper cache
```

如果想自己设置时间，可以这样做：

```
git config credential.helper 'cache --timeout=3600'
```

这样就设置一个小时之后失效

长期存储密码：

```
git config --global credential.helper store
```

增加远程地址的时候带上密码也是可以的。(推荐)

```
http://yourname:password@git.oschina.net/name/project.git
```

补充：使用客户端也可以存储密码的。

如果你正在使用ssh而且想体验https带来的高速，那么你可以这样做： 切换到项目目录下 ：

```
cd projectfile/
```

移除远程ssh方式的仓库地址

```
git remote rm origin
```

增加https远程仓库地址

```
git remote add origin http://yourname:password@git.oschina.net/name/project.git
```


## 从零开始

在github上创建一个空的仓库,这次可以选择readme和gitignore文件

```shell
git clone ssh地址
使用IDE打开这个根目录作为项目的根目录
```



