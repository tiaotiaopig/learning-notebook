# linux简单学习

## 常用操作
---
序号|命令|对应英文|作用
:-:|:-:|:-:|:-:
01|	ls|	list|	查看当前文件夹下的内容
02|	pwd|	print wrok directory	|查看当前所在文件夹
03|	cd [目录名]	|change directory	|切换文件夹
04|	touch [文件名]	|touch	|如果文件不存在，新建文件
05|	mkdir [目录名]	|make directory	|创建目录
06|	rm [文件名]|	remove	|删除指定的文件名
07|	clear|	clear|	清屏

+ 在敲出 文件／目录／命令 的前几个字母之后，按下 tab 键
    + 如果输入的没有歧义，系统会自动补全
    + 如果还存在其他 文件／目录／命令，再按一下 tab 键，系统会提示可能存在的命令
    小技巧

+ 按 上／下 光标键可以在曾经使用过的命令之间来回切换,如果想要退出选择，并且不想执行当前选中的命令，可以按 ctrl + c

+ 终端命令格式:

    > `command [-options] [parameter]`
    + **command**：命令名，相应功能的英文单词或单词的缩写
    + **[-options]**：选项，可用来对命令进行控制，也可以省略
    + **[parameter]**：传给命令的参数，可以是 零个、一个 或者 多个
    + []:表示可选

+ 帮助信息

    1. `command --help`

    2. `man command`
        + f,空格键:下一页
        + b:上一页
        + q:退出


## tar命令的使用
---
+ 一般而言，以“.gz”结尾的是以gzip方式进行压缩的，以".bz2"结尾的是以bzip2方式压缩的。

+ tar命令有5个常用的选项：

    1 “c”:表示创建,用来生成文件包;

    2 “x”:表示提取,从文件包中提取文件;

    3 “z”:使用 gzip 方式进行处理,它与“c”结合就表示压缩,与“x”结合就表示解压缩;

    4 “j”:使用 bzip2 方式进行处理,它与“c”结合就表示压缩,与“x”结合就表示解压缩;

    5、“v” : 即view，是可视化的意思，想看解压的文件进度就加上v。

    6 “f”:表示文件,后面接着一个文件名。

+ 示例(解压与压缩)
   1. 解压到目标路径:
   
   >  ~~~sh
   >  sudo tar -xzvf ~/download/in.tar.gz -C /home/target
   >  ~~~
   >
   >  
   2. 压缩文件到当前路径:
   
   > ~~~shell
   > sudo tar -czvf archive.tar.gz foo.txt bar.txt
   > ~~~

## 文件相关操作

+ Linux下文件和目录的特点

    + Linux 文件 或者 目录 名称最长可以有 256 个字符
    + 以 . 开头的文件为隐藏文件，需要用 -a 参数才能显示
    + . 代表当前目录
    + .. 代表上一级目录

## ssh远程登录

+ 登录远程主机(默认22端口):`ssh lifeng@192.168.54.65`

+ 使用公钥登录

    1. 在本机生成公钥对

        `ssh-keygen -t rsa   #-t表示类型选项，这里采用rsa加密算法`

    2. 将公钥复制到远程主机中

        `ssh-copy-id lifeng@192.168.54.65`

## scp上传下载

> linux scp 命令用于 Linux 之间复制文件和目录。
>
> scp 是 secure copy 的缩写, scp 是 linux 系统下基于 ssh 登陆进行安全的远程文件拷贝命令。
>
> scp 是加密的，[rcp](https://www.runoob.com/linux/linux-comm-rcp.html) 是不加密的，scp 是 rcp 的加强版。

### 语法

```bash
scp [-1246BCpqrv] [-c cipher] [-F ssh_config] [-i identity_file]
[-l limit] [-o ssh_option] [-P port] [-S program]
[[user@]host1:]file1 [...] [[user@]host2:]file2
```

简易写法:

```bash
scp [可选参数] file_source file_target 
```

**参数说明：**

- -1： 强制scp命令使用协议ssh1
- -2： 强制scp命令使用协议ssh2
- -4： 强制scp命令只使用IPv4寻址
- -6： 强制scp命令只使用IPv6寻址
- -B： 使用批处理模式（传输过程中不询问传输口令或短语）
- -C： 允许压缩。（将-C标志传递给ssh，从而打开压缩功能）
- -p：保留原文件的修改时间，访问时间和访问权限。
- -q： 不显示传输进度条。
- -r： 递归复制整个目录。
- -v：详细方式显示输出。scp和ssh(1)会显示出整个过程的调试信息。这些信息用于调试连接，验证和配置问题。
- -c cipher： 以cipher将数据传输进行加密，这个选项将直接传递给ssh。
- -F ssh_config： 指定一个替代的ssh配置文件，此参数直接传递给ssh。
- -i identity_file： 从指定文件中读取传输时使用的密钥文件，此参数直接传递给ssh。
- -l limit： 限定用户所能使用的带宽，以Kbit/s为单位。
- -o ssh_option： 如果习惯于使用ssh_config(5)中的参数传递方式，
- -P port：注意是大写的P, port是指定数据传输用到的端口号
- -S program： 指定加密传输时所使用的程序。此程序必须能够理解ssh(1)的选项。

### 实例

#### 1、从本地复制到远程

命令格式：

```bash
scp local_file remote_username@remote_ip:remote_folder 
# 或者 
scp local_file remote_username@remote_ip:remote_file 
# 或者 
scp local_file remote_ip:remote_folder 
# 或者 
scp local_file remote_ip:remote_file 
```

- 第1,2个指定了用户名，命令执行后需要再输入密码，第1个仅指定了远程的目录，文件名字不变，第2个指定了文件名；
- 第3,4个没有指定用户名，命令执行后需要输入用户名和密码，第3个仅指定了远程的目录，文件名字不变，第4个指定了文件名；

应用实例：

```bash
scp /home/space/music/1.mp3 root@www.runoob.com:/home/root/others/music 
scp /home/space/music/1.mp3 root@www.runoob.com:/home/root/others/music/001.mp3 
scp /home/space/music/1.mp3 www.runoob.com:/home/root/others/music 
scp /home/space/music/1.mp3 www.runoob.com:/home/root/others/music/001.mp3 
```

复制目录命令格式：

```bash
scp -r local_folder remote_username@remote_ip:remote_folder 
或者 
scp -r local_folder remote_ip:remote_folder 
```

- 第1个指定了用户名，命令执行后需要再输入密码；
- 第2个没有指定用户名，命令执行后需要输入用户名和密码；

应用实例：

```bash
scp -r /home/space/music/ root@www.runoob.com:/home/root/others/ 
scp -r /home/space/music/ www.runoob.com:/home/root/others/ 
```

上面命令将本地 music 目录复制到远程 others 目录下。

#### 2、从远程复制到本地

从远程复制到本地，只要将从本地复制到远程的命令的后2个参数调换顺序即可，如下实例

应用实例：

```
scp root@www.runoob.com:/home/root/others/music /home/space/music/1.mp3 
scp -r www.runoob.com:/home/root/others/ /home/space/music/
```

### 说明

1.如果远程服务器防火墙有为scp命令设置了指定的端口，我们需要使用 -P 参数来设置命令的端口号，命令格式如下：

```
#scp 命令使用端口号 4588
scp -P 4588 remote@www.runoob.com:/usr/local/sin.sh /home/administrator
```

2.使用scp命令要确保使用的用户具有可读取远程服务器相应文件的权限，否则scp命令是无法起作用的。
