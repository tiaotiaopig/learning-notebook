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

## 创建根目录

The first step in configuring any Vagrant project is to create a [Vagrantfile](https://www.vagrantup.com/docs/vagrantfile/). The Vagrantfile allows you to:

- Mark the root directory of your project. Many of the configuration options in Vagrant are relative to this root directory.
- Describe the kind of machine and resources you need to run your project, as well as what software to install and how you want to access it.

### Create a directory

Make a new directory for the project you will work on throughout these tutorials.

```shell-session
$ mkdir vagrant_getting_started
```

Move into your new directory.

```shell-session
$ cd vagrant_getting_started
```

### Initialize the directory

Vagrant has a built-in command for initializing a directory, `vagrant init`, which can take a box name and URL as arguments. Initialize the directory and specify the `hashicorp/bionic64` box.

```shell-session
$ vagrant init hashicorp/bionic64
```

## 安装沙盒(虚拟机)

简单说就是,从官方仓库添加一个操作系统镜像,全局可用

如果不添加,在初始化项目的时候,也会自动下载

``` bash
# 初始化项目,会创建 Vagrantfile,
# 指定hashicorp/bionic64(某个版本的操作系统)
# 这里是Ubuntu18.04
vagrant init hashicorp/bionic64
# 创建项目,自动下载镜像
vagrant up
```

Vagrant installed one when you initialized your project. Sometimes you may want to install a box without creating a new **Vagrantfile**. For this you would use the `box add` subcommand.

You can add a box to Vagrant with `vagrant box add`. This stores the box under a specific name so that multiple Vagrant environments can re-use it. If you have not added a box yet, do so now. Vagrant will prompt you to select a provider. Type `2` and press `Enter` to select Virtualbox.

```shell-session
$ vagrant box add hashicorp/bionic64
```

This will download the box named `hashicorp/bionic64` from [HashiCorp's Vagrant Cloud box catalog](https://vagrantcloud.com/boxes/search), where you can find and host boxes.

Boxes are globally stored for the current user. Each project uses a box as an initial image to clone from, and never modifies the actual base image. This means that if you have two projects both using the `hashicorp/bionic64` box you just added, adding files in one guest machine will have no effect on the other machine.

In the above command, you will notice that boxes are namespaced. Boxes are broken down into two parts—the username and the box name—separated by a slash. In the example above, the username is `hashicorp`, and the box is `bionic64`. You can also specify boxes via URLs or local file paths, but that will not be covered in the getting started guide.

## Use a Box

Now you've added a box to Vagrant either by initializing or adding it explicitly, you need to configure your project to use it as a base. Open the `Vagrantfile` and replace the contents with the following.

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/bionic64"
end
```

The `hashicorp/bionic64` in this case must match the name you used to add the box above. This is how Vagrant knows what box to use. If the box was not added before, Vagrant will automatically download and add the box when it is run.

You may specify an explicit version of a box by specifying `config.vm.box_version` for example:

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/bionic64"
  config.vm.box_version = "1.0.282"
end
```

You may also specify the URL to a box directly using `config.vm.box_url`:

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/bionic64"
  config.vm.box_url = "https://vagrantcloud.com/hashicorp/bionic64"
end
```

## 初始化环境

Now that you have initialized your project and configured a box for it to use, it is time to boot your first Vagrant environment.

### Bring up a virtual machine

Run the following from your terminal:

```shell-session
$ vagrant up
```

In less than a minute, this command will finish and you will have a virtual machine running Ubuntu.

### SSH into the machine

You will not actually *see* anything though, since Vagrant runs the virtual machine without a UI. To prove that it is running, you can SSH into the machine:

```shell-session
$ vagrant ssh
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-58-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Jun 16 21:57:57 UTC 2020

  System load:  0.44              Processes:           91
  Usage of /:   2.5% of 61.80GB   Users logged in:     0
  Memory usage: 11%               IP address for eth0: 10.0.2.15
  Swap usage:   0%

 * MicroK8s gets a native Windows installer and command-line integration.

     https://ubuntu.com/blog/microk8s-installers-windows-and-macos

0 packages can be updated.
0 updates are security updates.


vagrant@vagrant:~$
```

This command will drop you into a full-fledged SSH session. Go ahead and interact with the machine and do whatever you want. Although it may be tempting, be careful about `rm -rf /`, since Vagrant shares a directory at `/vagrant` with the directory on the host containing your Vagrantfile, and this can delete all those files. Shared folders will be covered in the next section.

Take a moment to think what just happened: With just one line of configuration and one command in your terminal, we brought up a fully functional, SSH accessible virtual machine. Cool.

Terminate the SSH session with `CTRL+D`, or by logging out.

```shell-session
vagrant@vagrant:~$ logout
Connection to 127.0.0.1 closed.
```

### Destroy the machine

Once you're back on your host machine, stop the machine that Vagrant is managing and remove all the resources created during the machine-creation process. When prompted, confirm with a `yes`.

```shell-session
$ vagrant destroy
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Forcing shutdown of VM...
==> default: Destroying VM and associated drives...
```

### Remove the box

The `vagrant destroy` command does not remove the downloaded box file. List your box files.

```shell-session
$ vagrant box list
hashicorp/bionic64  (virtualbox, 1.0.282)
```

Remove the box file with the `remove` subcommand, providing the name of your box.

```shell-session
$ vagrant box remove hashicorp/bionic64
Removing box 'hashicorp/bionic64' (v1.0.282) with provider 'virtualbox'...
```

## 本地和虚拟机文件同步

> 虚拟机中的**/vagrant** 目录,映射到本地的**Vagrantfile**所在目录下
>
> By default, Vagrant shares your project directory (the one containing the Vagrantfile) to the `/vagrant` directory in your guest machine.

## 构建一个web服务器

### Create an HTML directory

Create a directory where Apache will look for your content.

记住:**Vagrantfile文件所在目录为根目录**

```shell-session
$ mkdir html
```

Next create a file called `index.html` in the new directory, and populate it with the content for the index page.

```html
<!DOCTYPE html>
<html>
  <body>
    <h1>Getting started with Vagrant!</h1>
  </body>
</html>
```

### Write a provisioning script

We will setup [Apache](http://httpd.apache.org/) using a shell script for this tutorial. Create the following shell script and save it as `bootstrap.sh` in the same directory as your Vagrantfile.

```bash
#!/usr/bin/env bash

apt-get update
apt-get install -y apache2
if ! [ -L /var/www ]; then
  rm -rf /var/www
  ln -fs /vagrant /var/www
fi
```

This script will download and start Apache, and crate a symlink between your synced files directory and the location where Apache will look for content to serve.

### Configure Vagrant

Next, configure Vagrant to run this shell script when setting up the machine, by editing the Vagrantfile. It which should now look like this.

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/bionic64"
  config.vm.provision :shell, path: "bootstrap.sh"
end
```

The `provision` line tells Vagrant to use the `shell` provisioner to setup the machine, with the `bootstrap.sh` file. The file path is relative to the location of the project root (where the Vagrantfile is).

### Provision the webserver

If your machine were not already up, `vagrant up` would create your machine and Vagrant would automatically provision it. But, because the guest machine is already running from a previous step, You need to reload your machine.

```shell-session
$ vagrant reload --provision
==> default: Attempting graceful shutdown of VM...
==> default: Checking if box 'hashicorp/bionic64' version '1.0.282' is up to date...
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
## ... Output truncated ...
```

This will quickly restart your virtual machine, skipping the initial import step. The provision flag on the reload command instructs Vagrant to run the provisioners, since usually Vagrant will only do this on the first `vagrant up`.

After Vagrant completes running, the web server will be up and running. You cannot see the website from your own browser (yet), but you can verify that the provisioning works by loading a file from within the machine.

SSH into the guest machine.

```shell-session
$ vagrant ssh
```

Now get the HTML file that was created during provisioning.

```shell-session
vagrant@vagrant:~$ wget -qO- 127.0.0.1
<!DOCTYPE html>
<html>
  <body>
    <h1>Getting started with Vagrant!</h1>
  </body>
</html>
```

This works because in the shell script above you installed Apache and setup the default `DocumentRoot` of Apache to point to your `/vagrant` directory, which is the default synced folder setup by Vagrant.

## 配置网络

配置主机端口和虚拟机端口的之间映射，以便在主机上可访问虚拟机上运行的服务

### Configure port forwarding

Port forwarding allows you to specify ports on the guest machine to share via a port on the host machine. This allows you to access a port on your own machine, but actually have all the network traffic forwarded to a specific port on the guest machine.

Set up a forwarded port so you can access Apache in your guest, by adding it to the Vagrantfile under the line you added to run your bootstrap script. Below is the full file with port forwarding added.

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/bionic64"
  config.vm.provision :shell, path: "bootstrap.sh"
  config.vm.network :forwarded_port, guest: 80, host: 4567
end
```

Reload so that these changes can take effect.

```shell-session
$ vagrant reload
```

### Access the served files

Once the machine is running again, load [`http://127.0.0.1:4567`](http://127.0.0.1:4567/) in your browser, where you will find a web page that is being served from the guest virtual machine.

## 分享环境

通过一个内网穿透工具(**ngrok**),将本地服务通过一个url暴露到公网上