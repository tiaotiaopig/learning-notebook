# 使用git将本地仓库推送到github

## github上创建远程仓库

> 1. 登录后在用户的主界面,右上角点击加号 -> New Repository -> 设置仓库名称,是否私有,其他的就不要点了
>
> 2. 设置ssh秘钥,右上角点击用户头像 -> setting -> SSH and GPG keys
>
> 3. ```shell
>     ssh-keygen -t rsa -C "youremail@example.com"
>     注意：这个邮箱地址写你自己的
>     ```
>
> Downloads and installs xclip. If you don't have `apt-get`, you might need to use another installer (like `yum`)
>
>     sudo apt-get install xclip
>     
>     # Copies the contents of the id_rsa.pub file to your clipboard
>     xclip -selection clipboard < ~/.ssh/id_rsa.pub
>     ```
>
> 4. 接着你会看到一个叫.ssh的目录，里面有两个文件， id_rsa 和 id_rsa.pub，秘钥就在id_rsa.pub文件里，打开它，然后复制 ，粘贴到github的哪个秘钥大框框里，标题随便写，最后点击Add ssh key即可。
>
>     注:这里查看私钥的密码可以不设置(lifeng我设置的密码)

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

## 更改远端仓库URL

1. 从 ssh 转换到 https

```shell
# 进入工作目录
cd workspace
# 查看远程仓库信息
git remote -v
# 更改为https链接
git remote set-url origin https://github.com/USERNAME/REPOSITORY.git
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


## 从零开始

在github上创建一个空的仓库,这次可以选择readme和gitignore文件

```shell
git clone ssh地址
使用IDE打开这个根目录作为项目的根目录
```



