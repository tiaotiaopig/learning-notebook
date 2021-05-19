# VS 使用 WSL 环境

## 基本操作

> 直接安装 `Remote -WSL` 插件
>
> 然后再wsl命令行中操作
>
> ~~~bash
> cd my-app # 进入项目的根目录
> code . # 使用 code 打开当前目录
> ~~~
>
> 会提示安装一些东西,安装完成后即可,在win10 中的 VS 打开
>
> 终端快捷键: **ctrl + `** ,这样就可以在 VS 运行 wsl 的命令行

## 配置node环境(js开发)

1. 在 **WSL** 中安装 nodejs 环境

   ```bash
   # 1. Remove the old PPA if it exists
   # This step is only required if you previously used Chris Lea's Node.js PPA.
   
   # add-apt-repository may not be present on some Ubuntu releases:
   # sudo apt-get install python-software-properties
   sudo add-apt-repository -y -r ppa:chris-lea/node.js
   sudo rm -f /etc/apt/sources.list.d/chris-lea-node_js-*.list
   sudo rm -f /etc/apt/sources.list.d/chris-lea-node_js-*.list.save
   
   # 2. Add the NodeSource package signing key
   curl -fsSL https://deb.nodesource.com/gpgkey/nodesource.gpg.key | sudo apt-key add -
   # wget can also be used:
   # wget --quiet -O - https://deb.nodesource.com/gpgkey/nodesource.gpg.key | sudo apt-key add -
   
   # 3. Add the desired NodeSource repository
   # Replace with the branch of Node.js or io.js you want to install: node_6.x, node_8.x, etc...
   VERSION=node_16.x
   # The below command will set this correctly, but if lsb_release isn't available, you can set it manually:
   # - For Debian distributions: jessie, sid, etc...
   # - For Ubuntu distributions: xenial, bionic, etc...
   # - For Debian or Ubuntu derived distributions your best option is to use the codename corresponding to the upstream release your distribution is based off. This is an advanced scenario and unsupported if your distribution is not listed as supported per earlier in this README.
   DISTRO="$(lsb_release -s -c)"
   echo "deb https://deb.nodesource.com/$VERSION $DISTRO main" | sudo tee /etc/apt/sources.list.d/nodesource.list
   echo "deb-src https://deb.nodesource.com/$VERSION $DISTRO main" | sudo tee -a /etc/apt/sources.list.d/nodesource.list
   
   # 4. Update package lists and install Node.js
   sudo apt-get update
   sudo apt-get install nodejs
   ```

### vue.js 简单使用

