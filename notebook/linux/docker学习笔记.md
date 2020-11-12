# Docker学习笔记

## Docker是什么

    Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议开源。

    Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

    容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

    Docker 从 17.03 版本之后分为 CE（Community Edition: 社区版） 和 EE（Enterprise Edition: 企业版），我们用社区版就可以了。

    个人理解:容器和虚拟机比较类似,虚拟机有自己的操作系统,需要预分配资源并且独占,可以实现系统级隔离;docker容器是一种新的实现操作系统虚拟化的技术,拥有启动快,占用资源少,更加轻量等优点.

    容器技术是和我们的宿主机共享硬件资源及操作系统，可以实现资源的动态分配。容器包含应用和其所有的依赖包，但是与其他容器共享内核。容器在宿主机操作系统中，在用户空间以分离的进程运行。通过使用容器，我们可以轻松打包应用程序的代码、配置和依赖关系，将其变成容易使用的构建块，从而实现环境一致性、运营效率、开发人员生产力和版本控制等诸多目标。

---

## Docker的安装(ubuntu18.04为例)

+ 前期准备

    1. 安装需要的包

        `sudo apt install apt-transport-https ca-certificates software-properties-common curl`
    
    2. 卸载旧版本(若无可忽略)

        Docker 的旧版本被称为 docker，docker.io 或 docker-engine 。如果已安装，请卸载它们：

        `sudo apt-get remove docker docker-engine docker.io containerd runc`

        当前称为 Docker Engine-Community 软件包 docker-ce 。

+ 脚本安装(方便快捷)

    1. 亲测可用:

        `curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun`
    
    2. 使用国内 daocloud 一键安装命令:
    
         `curl -sSL https://get.daocloud.io/docker | sh`
---
## Docker简单入门

+ 仓库,镜像,容器

    > 镜像可以理解为环境的安装包,例如mysql镜像,也可以是一系列依赖软件的集合.

    > 仓库(repority)和github比较类似,是存储镜像的地方.可以有本地仓库和公共仓库,我们可以通过docker从仓库中直接下载镜像.

    > 容器有点类似虚拟机,我们可以创建容器,然后在容器中运行下载好的镜像,从而我们的应用程序可以在容器中运行.

+ 常用命令

1. run(使用指定的镜像创建一个容器并启动)
2. stop(停止指定的正在运行的容器)
3. start(启动容器)
3. rm(移除一个容器)
4. exec(进入已启动的容器)
5. ps(列出所有容器)
6. images(列出本地镜像,镜像名由软件名和版本号组成,使用冒号连接)
7. rmi(删除镜像)
8. pull(从仓库拉取镜像到本地,类似下载镜像)
9. push(本地镜像推送到仓库)

---
## Docker安装Mysql(ubuntu18.04)

1. 确定安装版本

    不加版本号默认是最新版本mysql:latest,
    访问镜像仓库:
    > <https://hub.docker.com/_/mysql?tab=tags>
    
    选择安装版本,使用以下命令拉取镜像:
    > `docker pull mysql:5.7.31`

    完成后可以使用以下命令查看本地镜像:
    > `docker images`

2. 创建容器并启动,添加镜像

    > ```docker run --name mysql-test -p 3306:3306 -e MYSQL_ROOT_PASSWORD=Lzslov123! -d mysql```

    命令参数说明:
    > --name:指定所要创建的容器名称
    
    > -p:指定端口映射，格式为：主机(宿主)端口:容器端口

    > -e:指定环境变量,MYSQL_ROOT_PASSWORD设置root用户密码

    > -d:使用镜像创建容器,并返回容器ID

    说明:上述命令仅是使用镜像创建容器并启动,里面的软件还不能使用

    查看已启动的容器:
    > `docker ps`
    >> -a:查看所有容器

3. 进入容器

    > `docker exec -it mysql-test bash`
    >> -it:在容器中以交互方式执行   
    >> mysql-test是启动的容器名     
    >> bash是在容器中执行bash终端       
    >> 自此我们我们进入容器mysql-test,并以交互方式执行bash   
   
    退出容器,但容器仍然是启动的,可以提供服务

     > `exit`


4. 使用软件

    登录MySQL
    > `mysql -u root -p`

    输入密码后就可以命令的方式操作MySQL了

    退出MySQL
    > `exit`