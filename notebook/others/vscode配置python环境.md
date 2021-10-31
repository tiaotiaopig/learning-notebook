# vscode 配置python环境

## 安装python插件

![image-20211031155750931](https://i.loli.net/2021/10/31/oflE5iQOMuyqkJ8.png)

## 安装python解释器

使用Anaconda和WSL的环境，或者本机安装python

```bash
# 查看python环境是否安装成功
python3 --version
```

## 创建workspace文件夹

使用vscode打开文件夹，或者命令行的方式，**vscode会默认文件夹根目录作为workspace**,这点非常重要

在debug的时候，要配置好路径，才能正常debug

```bash
# 创建项目根目录
mkdir hello
cd hello
# 使用vscode打开当前文件夹
code .
```

打开后，vscode会将这个文件夹作为worksapce，并将当前项目的配置存放在`.vscode/settings.json`

这和全局存储的用户设置是分离的

## 选择python解释器

> 有两种设置方式：
>
> 1. `Ctrl+Shift+P`,搜索`Python: Select Interpreter`
> 2. 打开一个`.py`文件，左下角设置
>
> 注意，如果使用的anaconda，环境名会有前缀，例如：`('base':conda)`

只有创建`.py`结尾的文件，vscode才会识别为python项目，

## 运行

> 1. 右键选择运行
> 2. 右上角运行按钮
> 3. 选择部分代码，右键运行

## debug

点击左边工具栏的小虫子即可，或者运行按钮的下拉框

通常我们需要一个debug配置文件，vscode会提示创建的，选择第一个pyton，会创建一个**launch.json**

![image-20211031162129728](https://i.loli.net/2021/10/31/lfhIaA74XvyujPs.png)

经常出问题的地方就是**相对路径**，默认是项目根目录，就是vscode打开的文件夹作为base

需要在aunch.json中配置`cwd:'${workspaceFolder}/data'`

