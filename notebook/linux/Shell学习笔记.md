# Shell学习笔记

## Bash简介
---
1. Shell的含义

    Shell 这个单词的原意是“外壳”，跟 kernel（内核）相对应，比喻内核外面的一层，即用户跟内核交互的对话界面。

    具体来说，Shell 这个词有多种含义。

    首先，Shell 是一个程序，提供一个与用户对话的环境。这个环境只有一个命令提示符，让用户从键盘输入命令，所以又称为命令行环境（commandline，简写为 CLI）。Shell 接收到用户输入的命令，将命令送入操作系统执行，并将结果返回给用户。本文中，除非特别指明，Shell 指的就是命令行环境。

    其次，Shell 是一个命令解释器，解释用户输入的命令。它支持变量、条件判断、循环操作等语法，所以用户可以用 Shell 命令写出各种小程序，又称为脚本（script）。这些脚本都通过 Shell 的解释执行，而不通过编译。

    最后，Shell 是一个工具箱，提供了各种小工具，供用户方便地使用操作系统的功能。

    ---

2. Shell的种类

    Shell 有很多种，只要能给用户提供命令行环境的程序，都可以看作是 Shell。

    历史上，主要的 Shell 有下面这些。

    Bourne Shell（sh）

    Bourne Again shell(bash)

    C Shell（csh）

    TENEX C Shell（tcsh）

    Korn shell（ksh）

    Z Shell（zsh）

    Friendly Interactive Shell（fish）
    
    Bash 是目前最常用的 Shell，除非特别指明，下文的 Shell 和 Bash 当作同义词使用，可以互换。

下面的命令可以查看当前运行的 Shell
> `echo $SHELL`

下面的命令可以查看当前的 Linux 系统安装的所有 Shell
> `cat /etc/shells`

**$** 是命令行环境的提示符

完整的提示符是 **[user@hostname] $**

注意，根用户（root）的提示符，不以美元符号（$）结尾，而以井号（#）结尾，用来提醒用户，现在具有根权限，可以执行各种操作，务必小心，不要出现误操作。这个符号是可以自己定义的

退出Shell终端
> `exit`

## Shell编程入门

### shell脚本编写规范

> 脚本文件后缀名: `*.sh`
> 首行格式规范: `#!/bin/bash` 配置shell解析器的类型
> 注释格式: `# 注释内容`

### 入门案例

```shell
#!/bin/bash
echo "hello world"
输出字符串到控制台
```

```shell
#!/bin/bash
mkdir ~/下载/mybash
cd ~/下载/mybash/
touch one.txt
echo "hello shell" >> one.txt
创建文件目录并进入,创建文本文件,将字符串输出到文件
```

### Shell变量

+ 系统环境变量
+ 自定义变量
+ 特殊符号变量

#### 系统环境变量

​	是Linux系统加载Shell的配置文件中定义的变量,共享给所有的Shell程序使用

​	分为全局配置文件和个人配置文件,分别对应系统级环境变量和用户级环境变量

​	查看命令`env`和`set`

#### 常用系统环境变量

| 变量名称 | 含义                                                        |
| -------- | ----------------------------------------------------------- |
| PATH     | 与Windows中的环境变量path一样,设置命令的收缩路径,以冒号分割 |
| HOME     | 当前用户主目录~                                             |
| HISTFILE | 当前用户执行命令的历史列表                                  |
| LANG     | 系统字符集                                                  |

变量访问方式:

	+ `$var_name`
	+ `${var_name}`    可以拼接字符串

变量删除:

	+ `unset var_name`

变量赋值:

	+ `var_name=value`	赋值号两边不能有空格

默认类型为字符串,无法进行数值运算,如果包含有空格,需要用双引号包裹

自定义常量:

+ `readonly var_name`

全局变量:

+ `export var_name`

在父Shell脚本文件中定义,在子Shell脚本文件中可以使用

#### 特殊变量

`$n`

+ `$0`当前Shell脚本文件名
+ `$1~$9`第一到第九个输入参数
+ `${10}`第十个参数



