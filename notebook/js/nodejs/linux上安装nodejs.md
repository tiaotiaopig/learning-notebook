# Linux 安装 nodejs

## 步骤

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
   ```

   

