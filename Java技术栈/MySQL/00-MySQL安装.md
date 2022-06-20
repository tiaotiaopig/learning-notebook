## MySQL 安装

去官网下载压缩包形式的安装包，自己解压，方便配置管理	

MySQL官网：https://dev.mysql.com/downloads/mysql/

## Windows

1. **mysqld启动**。

   在MySQL安装目录下的bin目录下有一个mysqld可执行文件， 在命令行里输入mysqld， 或者直接双击运行它就算启动了MySQL服务器程序了 。

2. 以服务方式运行服务器程序。

   我们需要长时间的运行某个程序， 而且需要在计算机启动的时候便启动它， 一般我们都会把它注册为一个Windows 服务， 操作系统会帮我们管理它。 把某个程序注册为Windows服务的方式挺简单， 如下：
   `"完整的可执行文件路径" --install [-manual] [服务名]`
   其中的-manual可以省略， 加上它的话表示在Windows系统启动的时候不自动启动该服务， 否则会自动启动。 

   服务名也可以省略， 默认的服务名就是MySQL。

   如果我们把bin目录添加到了环境变量，那么可以使用以下方式启动：

   > 1. `mysqld --help --verbose`,自己查一下MySQL的安装命令，我的是8.0.27，可以在**管理员运行**下面的命令：
   >
   >    ```bash
   >    # 创建数据文件目录（data文件夹）和mysql系统数据库 产生空root密码
   >    --initialize-insecure
   >    # 安装以手动方式启动，省略服务名，会使用默认的
   >    mysqld --install-manual [service_name]
   >    # 自启动
   >    mysqld --install [service_name]
   >    # 查看所有服务
   >    services.msc
   >    # 删除服务（通用）
   >    SC delete service_name
   >    # 移除MySQL服务
   >    mysqld --remove [service_name]
   >    ```
   >
   > 2. 我的安装流程，服务器程序（**mysqld**）启动
   >
   >    ```bash
   >    # 1.数据库初始化，必须要有，否则启动不起来
   >    # --initialize-insecure没有密码，还是不使用这个啦	
   >    mysqld --initialize-insecure
   >    mysqld --initialize
   >    # 2.安装服务，手动启动
   >    mysqld --install-manual
   >    # 3.启动MySQL服务,2中未指定服务名，默认名：MySQL
   >    net start MySQL
   >    # 4.关闭服务
   >    net stop MySQL
   >    # 删除服务
   >    mysqld --remove
   >    ```
   >
   > 3. 启动客户端程序（mysql）
   >
   >    ```bash
   >    # 以root用户启动
   >    mysql -uroot -p
   >    ```
   >
   > 4. 修改密码，如果是以不安全方式初始化的话
   >
   >    ```bash
   >    mysqladmin -u root password "password"
   >    ```

## Linux

