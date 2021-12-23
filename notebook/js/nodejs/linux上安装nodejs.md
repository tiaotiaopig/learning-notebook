# Linux 安装 nodejs

## 手动安装

1. 去官网下载[压缩包](https://nodejs.org/en/download/)

   或者使用命令：`wget https://nodejs.org/dist/v14.17.1/node-v14.17.1-linux-x64.tar.xz`

2. 解压缩要安装的目录

   ```bash
   cd /usr/develop
   # 解压时一定不要用sudo,会提高文件的权限，git 同理
   tar xvf nodejs.org/dist/v14.17.1/node-v14.17.1-linux-x64.tar.xz
   # bin 目录下包含node npm等命令
   ```

3. 修改环境变量

   ```bash
   # 修改重要文件之前，先备份
   cp /etc/profile /etc/profile.bak
   #或者vim 修改
   export PATH=$PATH:/root/node-v12.18.1-linux-x64/bin
   # 让修改生效
   source /etc/profile
   
   sudo vim ~/.zshrc
   
   # 添加以下内容
   # nodejs environment config
   export NODEJS_HOME=/usr/local/develop/node-v14.17.3-linux-x64
   export PATH=.${NODEJS_HOME}/bin:$PATH
   
   source ~/.zshrc
   ```

## nvm安装

**nvm**(node.js version manager)是node.js的版本管理工具，其实就是shell脚本，傻瓜式安装卸载更新node.js

1. 运行nvm安装脚本

   ```bash
   # clone nvm git reporitory
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
   ```

   上述就是将nvm仓库clone到用户根目录，node.js的各个版本就放在这个文件夹下

2. 安装node.js

   ```bash
   # 查看有用命令 
   nvm -h
   # 安装lts版node.js
   nvm install --lts
   ```

   就是这么简单，要是所有的工具都这么简单就好啦

3. 卸载

   ```bash
   nvm uninstall <version> # Uninstall a version
   nvm uninstall --lts # Uninstall using automatic LTS (long-term support) alias `lts/*`, if available.
   nvm unload # Unload `nvm` from shell
   ```

   > to remove, delete, or uninstall nvm - just remove the `$NVM_DIR` folder (usually `~/.nvm`)
