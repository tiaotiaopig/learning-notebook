# vagrant 快速入门

## 快速开始

**vagrant** 自动化运维工具，目前理解，可以创建虚拟机，配合**ansible**可以快速搭建虚拟开发环境

没有docker 好用，感觉已经过时啦

### 安装

- Install the latest version of [Vagrant](https://www.vagrantup.com/docs/installation/).
- Install [VirtualBox](https://www.virtualbox.org/)

### 上手体验

After you run the following two commands, you will have a fully running virtual machine in [VirtualBox](https://www.virtualbox.org/) running [Ubuntu 18.04 LTS 64-bit](https://app.vagrantup.com/hashicorp/boxes/bionic64).

Initialize Vagrant.

```shell-session
$ vagrant init hashicorp/bionic64
```

Start the virtual machine.

```shell-session
$ vagrant up
```

SSH into this machine with `vagrant ssh`, and explore your environment. Leave the SSH session with `logout`.

When you are done exploring terminate the virtual machine, and confirm when the CLI prompts you by typing `yes`.

```shell-session
$ vagrant destroy
```