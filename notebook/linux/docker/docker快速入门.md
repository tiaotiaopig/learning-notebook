# docker快速入门(官方文档)

## 安装docker engine

### 卸载旧版本

老版本的`docker`叫`docker`, `docker.io`,或者`docker-engine`,如果安装过,就先卸载它们

```bash
sudo apt-get remove docker docker-engine docker.io containerd runc
```

如果`apt`报这些包没有安装,也是可以的

`/var/lib/docker/`中的内容将会保留,它包含了`image`,`container`,`volumes`和`networks`.如果你不想保存现有的数据,想要一个全新的安装,[可以参考](https://docs.docker.com/engine/install/ubuntu/#uninstall-docker-engine)

### 使用仓库安装,初始化仓库

1. 更新`apt`包索引,然后安装一些依赖包,使用`apt`可以通过`Https`使用仓库

    ```bash
     # 更新包索引
     sudo apt-get update
     
     # 安装依赖包
     sudo apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg \
        lsb-release
    ```

    2. 添加docker官方GPG key

        ```bash
        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
        ```

    3. 添加docker仓库链接

        ```bash
        echo \
          "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
          $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
        ```

### 安装docker engine

1. 更新`apt`包索引,安装最新版本的`Docker Engine`和`containerd`

    ```bash
    sudo apt-get update
    sudo apt-get install docker-ce docker-ce-cli containerd.io
    ```

2. 安装历史版本(可选)

    ```bash
    apt-cache madison docker-ce
    sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io
    ```

    例如:`5:18.09.1~3-0~ubuntu-xenial`

3. 运行`hello-wrold`镜像,验证是否安装成功

    ```bash
     sudo docker run hello-world
    ```

### 不需要每次都`sudo`

> The Docker daemon binds to a Unix socket instead of a TCP port. By default that Unix socket is owned by the user `root` and other users can only access it using `sudo`. The Docker daemon always runs as the `root` user.
>
> If you don’t want to preface the `docker` command with `sudo`, create a Unix group called `docker` and add users to it. When the Docker daemon starts, it creates a Unix socket accessible by members of the `docker` group.

创建`docker`组,并将你的用户添加进去

```bash
# create the docker group
sudo groupadd docker
# Add your user to the docker group
sudo usermod -aG docker $USER
# Log out and log back in so that your group membership is re-evaluated.
newgrp docker 
# Verify that you can run docker commands without sudo
docker run hello-world
```

If you initially ran Docker CLI commands using `sudo` before adding your user to the `docker` group, you may see the following error, which indicates that your `~/.docker/` directory was created with incorrect permissions due to the `sudo` commands.

```none
WARNING: Error loading config file: /home/user/.docker/config.json -
stat /home/user/.docker/config.json: permission denied
```

To fix this problem, either remove the `~/.docker/` directory (it is recreated automatically, but any custom settings are lost), or change its ownership and permissions using the following commands:

```
$ sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
$ sudo chmod g+rwx "$HOME/.docker" -R
```

## 卸载Docker

1. Uninstall the Docker Engine, CLI, and Containerd packages:

    ```bash
    $ sudo apt-get purge docker-ce docker-ce-cli containerd.io
    ```

2. Images, containers, volumes, or customized configuration files on your host are not automatically removed. To delete all images, containers, and volumes:

    ```bash
    $ sudo rm -rf /var/lib/docker
    $ sudo rm -rf /var/lib/containerd
    ```

You must delete any edited configuration files manually.

## Start the tutorial

If you’ve already run the command to get started with the tutorial, congratulations! If not, open a command prompt or bash window, and run the command:

```cli
docker run -d -p 80:80 docker/getting-started
```

You’ll notice a few flags being used. Here’s some more info on them:

- `-d` - run the container in detached mode (in the background)
- `-p 80:80` - map port 80 of the host to port 80 in the container
- `docker/getting-started` - the image to use

> **Tip**
>
> You can combine single character flags to shorten the full command. As an example, the command above could be written as:
>
> ```
> docker run -dp 80:80 docker/getting-started
> ```

## The Docker Dashboard

Before going too far, we want to highlight the Docker Dashboard, which gives you a quick view of the containers running on your machine. The Docker Dashboard is available for Mac and Windows. It gives you quick access to container logs, lets you get a shell inside the container, and lets you easily manage container lifecycle (stop, remove, etc.).

To access the dashboard, follow the instructions for either [Mac](https://docs.docker.com/docker-for-mac/dashboard/) or [Windows](https://docs.docker.com/docker-for-windows/dashboard/). If you open the dashboard now, you will see this tutorial running! The container name (`jolly_bouman` below) is a randomly created name. So, you’ll most likely have a different name.

![Tutorial container running in Docker Dashboard](https://docs.docker.com/get-started/images/tutorial-in-dashboard.png)

## What is a container?

Now that you’ve run a container, what *is* a container? Simply put, a container is simply another process on your machine that has been isolated from all other processes on the host machine. That isolation leverages [kernel namespaces and cgroups](https://medium.com/@saschagrunert/demystifying-containers-part-i-kernel-space-2c53d6979504), features that have been in Linux for a long time. Docker has worked to make these capabilities approachable and easy to use.

## What is a container image?

When running a container, it uses an isolated filesystem. This custom filesystem is provided by a **container image**. Since the image contains the container’s filesystem, it must contain everything needed to run an application - all dependencies, configuration, scripts, binaries, etc. The image also contains other configuration for the container, such as environment variables, a default command to run, and other metadata.

We’ll dive deeper into images later on, covering topics such as layering, best practices, and more.

> **Info**
>
> If you’re familiar with `chroot`, think of a container as an extended version of `chroot`. The filesystem is simply coming from the image. But, a container adds additional isolation not available when simply using chroot.

