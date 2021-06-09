# AssetsView 部署

## 环境搭建

1. XAMPP下载

    我们使用PHP服务器组件XAMPP 5.6.40(就是开发环境的集成,减少配置)

    链接:[XAMPP](https://sourceforge.net/projects/xampp/files/XAMPP%20Linux/5.6.40/)

2. 安装和启动XAMPP

    ```bash
    # 添加执行权限
    sudo chmod +x xampp-linux-x64-5.6.40-1-installer.run
    # 安装xampp,完成后自动启动
    sudo ./xampp-linux-x64-5.6.40-1-installer.run
    # 安装目录为lampp
    cd lampp
    # 关闭后,再次启动,命令行方式
    sudo ./xampp
    # 图形化方式
    sudo ./manager-linux-x64.run
    ```

    安装过程一路next即可

3. 启动apache 和 MySql 服务

    浏览器访问`http://localhost:80`,出现正常界面,说明安装成功

## 项目部署

1. 进入部署目录

    我的xampp安装在`/opt/lampp`

    ```bash
    # htdocs/ 即为项目部署目录,只要将项目整个复制进去,即可完成部署
    cd /opt/lampp/htdocs
    # 导入项目
    sudo git clone https://github.com.cnpmjs.org/tiaotiaopig/AssetsView.git
    ```

2. 导入数据

    使用MySQL管理工具或者命令行方式运行sql脚本

    > username=root
    >
    > password='' # 密码为空
    >
    > 连接上后,创建数据库**assets_scan**,编码**utf8**
    >
    > 选择**assets_scan**数据库,执行sql脚本
    >
    > 位于**AssetsView/data/db/**目录下

3. 运行项目

    在浏览器输入:[AssetsView](http://localhost/AssetsView),即可看到项目运行

    用户名: assets    密码: 1q2w3e

    
