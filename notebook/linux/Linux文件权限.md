# Linux 文件权限

## 精简版

1. 命令

   ~~~shell
   chmod 777 picgo.jpg
   ~~~

   格式

   ~~~sh
   chmod 权限数字　文件名
   ~~~

说明：

>r 读权限 read 4
>
>w 写权限 write 2
>
>x 操作权限 execute 1
>
>权限数字对应权限组说明：
>
>总共分为4部分
>
>【文件或文件夹】【owner权限】【group权限】【others权限】
>
>【文件是-，文件夹是d】【r/w/x相加】【r/w/x相加】【r/w/x相加】
>
>Linux档案的基本权限就有九个，分别是owner/group/others三种身份各有自己的read/write/execute权限
>
>d rwx rwx rwx =777 表示目录的操作权限
>
>\- rwx rwx rwx = 777 表示文件的操作权限

![image-20210310210236562.png](https://i.loli.net/2021/03/10/KFrjomNfxeiVOck.png)

