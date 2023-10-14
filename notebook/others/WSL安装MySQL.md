# WSL安装MySQL

![image-20220801225338088](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/image-20220801225338088.png)

解决方案:

> sudo mkdir -p /var/run/mysqld
>
> sudo chown mysql /var/run/mysqld/

## 启动服务器和客户端

> 1. 启动服务器
>
>    `sudo mysqld_safe &` 后台启动MySQL服务器进程
>
> 2. 启动客户端
>
>    `sudo mysql -uroot -h localhost -p` 密码：123456
