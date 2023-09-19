# Ubuntu18.04必备的22款软件(安装详解)

以下是9012年11月，本人Ubuntu18.04安装的22款软件，不一定最全，但是软件都是最新的，附带安装教程+安装包下载，权当记录 + 分享！如果，对你有用，别忘了点个赞哦：）

- **1.搜狗输入法**
- **2.网易云音乐**
- **3.百度网盘**
- **4.福昕PDF阅读器**
- **5.Shutter截图**
- **6.Flameshot截图**
- **7.uGet+aria2**
- **8.金山WPS**
- **9.谷歌浏览器**
- **10.VLC视频播放器**
- **11.微信**
- **12.Teamview**
- **13.Vim**
- **14.Sublime Text**
- **15.JDK8**
- **16.Maven**
- **17.Postman**
- **18.IntelliJ IDEA**
- **19.Pycharm**
- **20.Anaconda**
- **21.MySQL8.0**
- **22.Navicat**

## 1.搜狗输入法

装机必备的软件，直接装就行无！无需提前装好Fcitx环境，因为装搜狗时会自动安装这个环境。

### 下载

[https://pinyin.sogou.com/linux/?r=pinyin](https://link.zhihu.com/?target=https%3A//pinyin.sogou.com/linux/%3Fr%3Dpinyin)

### 安装

安装相对来说比较容易，直接参考：[如何在Ubuntu系统中安装搜狗输入法](https://link.zhihu.com/?target=https%3A//jingyan.baidu.com/article/0a52e3f4fa2ba8bf63ed724d.html)

### 卸载

搜狗输入法刚开始安装有点问题，后来想卸载了重新用，结果没想到卸载带来了更大的问题，卸载完怎么装都显示不出来了......搜了半天发现是原来配置没有清理干净！注意，要加上参数-P或--purge,删除/净化程序及其配置文件

```bash
#1.卸载搜狗
sudo dpkg -P sogoupinyin
#2.卸载fcitx环境
可以sudo dpkg -P fcitx也可在Ubuntu软件中直接点卸载
#3.删除所有带rc标记的包
dpkg -l | grep ^rc | cut -d' ' -f3 | sudo xargs dpkg --purge
#4.用户～/.config/下删除所有和搜狗、fcitx相关的文件夹
SogouPY SogouPY.users sogou-qimpanel fcitx
```

## 2.网易云音乐

虽然歌曲库少了点，不过支持Linux,不像QQ音乐没有Linux版的，差评

### 下载

[https://music.163.com/#/download](https://link.zhihu.com/?target=https%3A//music.163.com/%23/download)

![img](https://pic1.zhimg.com/80/v2-1326f4710dc296b00bfd245283f04844_1440w.jpg)

### 安装

安装很简单，基本没踩坑

```bash
sudo dpkg -i  /your_path_to/etease-cloud-music_1.2.1_amd64_ubuntu_20190428.deb
# 如果没有安装成功，缺少依赖，则执行
sudo apt-get install -f
```

## 3.百度网盘

百度网盘这个神器还有Linux版的，不错！

### 下载

[https://pan.baidu.com/download/](https://link.zhihu.com/?target=https%3A//pan.baidu.com/download/)

![img](https://pic3.zhimg.com/80/v2-50cf2002c54673c07535be1d38d98532_1440w.jpg)

### 安装

安装很简单，基本没踩坑

```bash
sudo dpkg -i  /your_path_to/baidunetdisk_linux_2.0.2.deb
# 如果没有安装成功，缺少依赖，则执行
sudo apt-get install -f
```

## 4.福昕PDF阅读器

### 下载

[https://www.foxitsoftware.cn/downloads/](https://link.zhihu.com/?target=https%3A//www.foxitsoftware.cn/downloads/)

![img](https://pic1.zhimg.com/80/v2-4f0ccaf00a4c151cb1de9925f1ab2ea4_1440w.jpg)

### 安装

安装很简单，基本没踩坑,下载后直接解压缩，是个.run文件，可以直接双击运行安装

## 5.Shutter(截图+编辑软件)

搜了一下，大多推荐shutter这款截图软件，果断决定下一个

### 下载+安装

可以在Ubuntu自带的【Ubuntu软件】里搜索shutter下载，不过更推荐直接命令获取：

```bash
sudo apt install shutter
# 或sudo apt-get -i shutter
```

安装完成即可使用，不过通常18.04版本的shutter只有截图功能，没开启“编辑”功能，需要编辑的需要额外下载以下三个工具包：

- [libgoocanvas-common](https://link.zhihu.com/?target=https%3A//launchpad.net/ubuntu/%2Barchive/primary/%2Bfiles/libgoocanvas-common_1.0.0-1_all.deb)
- [libgoocanvas3](https://link.zhihu.com/?target=https%3A//launchpad.net/ubuntu/%2Barchive/primary/%2Bfiles/libgoocanvas3_1.0.0-1_amd64.deb)
- [libgoo-canvas-perl](https://link.zhihu.com/?target=https%3A//launchpad.net/ubuntu/%2Barchive/primary/%2Bfiles/libgoo-canvas-perl_0.06-2ubuntu3_amd64.deb)

然后运行：

```bash
dpkg -i /your_path_to/libgoocanvas-common_1.0.0-1_all.deb
dpkg -i /your_path_to/libgoocanvas3_1.0.0-1_amd64.deb
dpkg -i /your_path_to/libgoo-canvas-perl_0.06-2ubuntu3_amd64.deb
apt-get -f install
```

> 参考：[Ubuntu 18.04 上安装 Shutter 并启用 Edit 功能](https://link.zhihu.com/?target=https%3A//ijuer.com/ubuntu-18-04-%e4%b8%8a%e5%ae%89%e8%a3%85-shutter-%e5%b9%b6%e5%90%af%e7%94%a8-edit-%e5%8a%9f%e8%83%bd/)

## 6.Flameshot(截图+编辑软件)

### 下载+安装

这是一款同样推荐的截图软件，类似QQ截图那样挺方便的，截图+框选/注释等实用的编辑功能，项目在github开源：[https://github.com/lupoDharkael/flameshot](https://link.zhihu.com/?target=https%3A//github.com/lupoDharkael/flameshot) 同样可以在Ubuntu自带的【Ubuntu软件】里搜索shutter下载，不过更推荐直接命令获取：

```bash
sudo apt install flameshot
# 或sudo apt-get -i flameshot
```

## 7.uGet（下载神器）

在Ubuntu下想找迅雷，结果没找到，又不想安个虚拟机专门跑迅雷，于是推荐uGet，这是一个简化版的迅雷!

### 下载

[https://ugetdm.com/downloads/](https://link.zhihu.com/?target=https%3A//ugetdm.com/downloads/)

![img](https://pic3.zhimg.com/80/v2-6edf27b6e41dd3b94b3660b6687d5da6_1440w.jpg)

推荐官网直接下载，当然也可以在【Ubuntu软件】中直接下载，不过上面的版本有点老，而官网是2.2.1-stable最新版的。不过，先不要着急下载，**官网推荐用ppa方式安装↓**

### 安装

[https://ugetdm.com/downloads/ubuntu/](https://link.zhihu.com/?target=https%3A//ugetdm.com/downloads/ubuntu/)

![img](https://pic2.zhimg.com/80/v2-3c9019c99fb0988b91e42e7a2594bacd_1440w.jpg)

```bash
sudo add-apt-repository ppa:plushuang-tw/uget-stable
sudo apt update 
sudo apt install uget
```

### 安装aria2

uGet安装完成后，根据个人需要，可以安装和配置aria2，**如果不需要的，此步可以直接pass**。Aria2是一个命令行下载软件，配合uGet使用，效果更好。

> Aria2 是一个多平台轻量级，支持 HTTP、FTP、BitTorrent 等多协议、多来源的命令行下载工具。Aria2 可以从多个来源、多个协议下载资源，最大的程度上利用了你的带宽

```text
sudo apt install aria2 
# 安装成功后可用aria2c -v查看版本
```

### 配置aria2

主要是新建一个aria2.conf配置各种下载参数、上传下载速度限制、并发线程数、bt相关配置等。可参考我的配置：

[aria2.zip](https://link.zhihu.com/?target=https%3A//www.yuque.com/attachments/yuque/0/2019/zip/216914/1574233856867-7b9962f8-4a33-4b07-a1e2-3cfa930a1bc9.zip)，也可参考：[http://aria2c.com/usage.html](https://link.zhihu.com/?target=http%3A//aria2c.com/usage.html)

```text
#新建aria2文件夹 
sudo mkdir /etc/aria2 
#创建session文件 
sudo touch /etc/aria2/aria2.session 
sudo chmod 777 /etc/aria2/aria2.session 
#编辑配置文件 
sudo vim /etc/aria2/aria2.conf
```

创建并编辑了配置aria2.conf，就可以在shell中启动aria2，没有ERROR即表示安装+配置成功：

```text
sudo aria2c --conf-path=/etc/aria2/aria2.conf 
```

![img](https://pic1.zhimg.com/80/v2-cde275fe084620cfc7d5c7ef41f35030_1440w.jpg)

不过，通常我们不喜欢在shell中用aria2下载，我们需要将aria2添加到uGet中，在uGet中设置>插件：

![img](https://pic2.zhimg.com/80/v2-ac6f01fb33e32b375e9f7e1b23dff695_1440w.jpg)

可以设置为aria2、或者aria2+curl，然后就可以用uGet愉快地下载了～

## 8.金山WPS

Office有windows版、mac版本、唯独没有提供Linux版，于是WPS成为了主力，话说因为雷军的原因，个人对WPS还是挺有感情的，这么多年金山系列的软件都挺不错的！对了，WPS据说是当年求伯君一个人整出来的，太厉害了！！！莫名想到：鲁大师的第一代也是一位姓鲁的师傅开发出来的：）

### 下载

[https://www.wps.cn/product/wpslinux](https://link.zhihu.com/?target=https%3A//www.wps.cn/product/wpslinux)

![img](https://pic1.zhimg.com/80/v2-24e90c11bc7c954aec0f6cc28e072a4c_1440w.jpg)

### 安装

```bash
sudo dpkg -i /your_path_to/wps-office_11.1.0.8865_amd64.deb
```

这里需要注意，下载下来的WPS是需要字体支持的，需要手动安装，否则使用时会提示字体缺失，解决方法： 百度一下：ubuntu安装wps字体

## 9.谷歌浏览器

Ubuntu自带的火狐浏览器其实也不错了，不过谷歌用顺手了，还是下一个吧。

### 下载

[https://www.google.cn/chrome/](https://link.zhihu.com/?target=https%3A//www.google.cn/chrome/)

![img](https://pic3.zhimg.com/80/v2-23cc536000494ea46c562e0e2d197732_1440w.jpg)

### 安装

```bash
sudo dpkg -i /your_path_to/google-chrome-stable_current_amd64.deb
sudo apt-get -f install
```

## 10.VLC视频播放器

搜了一圈，发现VLC推荐的人挺多，下载一个，看视频必备。

### 下载+安装

[https://www.videolan.org/vlc/download-ubuntu.html](https://link.zhihu.com/?target=https%3A//www.videolan.org/vlc/download-ubuntu.html)

![img](https://pic3.zhimg.com/80/v2-bfcb74fa7eda2d4a29a7b1e63216145e_1440w.jpg)

官方给出了两种软件安装方式：

- **在【Ubuntu软件】中搜索“vlc”并安装；**
- **命令行执行 `% sudo snap install vlc`**

我用的是第二种方式

## 11.微信

很可惜，腾讯官方并没有提供QQ/微信的Linux版下载，于是只能在虚拟机的Windows中装软件，或者利用开源项目

### 下载+安装

[https://github.com/geeeeeeeeek/electronic-wechat/releases](https://link.zhihu.com/?target=https%3A//github.com/geeeeeeeeek/electronic-wechat/releases) 不过作者很久没更新了，怕后期不好用，我这里直接用的是微信网页版，不过改造一下看上去和桌面版的没什么不同：）网页版的改造方式参考：[在ubuntu中使用微信的三种方式](https://link.zhihu.com/?target=https%3A//segmentfault.com/a/1190000019478007%3Futm_source%3Dtag-newest)

## 12.Teamview

谁用谁知道，远程控制电脑不要太舒服：）

### 下载

[https://www.teamviewer.cn/cn/download/linux/](https://link.zhihu.com/?target=https%3A//www.teamviewer.cn/cn/download/linux/)

![img](https://pic1.zhimg.com/80/v2-3cd05161e45ee3428ac304459db0dd68_1440w.jpg)

### 安装

将下载好的deb包放在合适位置，譬如：/usr/local/software/

```bash
cd /usr/local/software/
sudo dpkg -i  teamviewer_14.7.1965_amd64.deb
# 如果报错或缺少依赖
sudo apt-get install -f
sudo dpkg -i  teamviewer_14.7.1965_amd64.deb
```

------

## 编程软件

## 13.Vim

这个不解释了，只要你用到shell,必装的一款软件

### 下载+安装

```bash
sudo apt-get install vim
```

## 14.Sublime Text

轻量又高效的文本编辑器，暗黑色风格很高大上

### 下载

[http://www.sublimetext.com/3](https://link.zhihu.com/?target=http%3A//www.sublimetext.com/3)

![img](https://pic3.zhimg.com/80/v2-11465155bb62b10c7d7b430119ca93ee_1440w.jpg)

### 安装

**方式一：snap安装**

```bash
# 安装Snap
sudo apt install snapd
# 安装Sublime text
sudo snap install sublime-text
```

**方式二：官方源安装**

```bash
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
# 安装必要组件
sudo apt-get install apt-transport-https
# 添加sublimetext的源
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
# 更新源
sudo apt-get update
# 修复缺失包
sudo apt-get install sublime-text --fix-missing
```

参考：[https://www.linuxidc.com/Linux/2019-03/157533.htm](https://link.zhihu.com/?target=https%3A//www.linuxidc.com/Linux/2019-03/157533.htm)

## 15.JDK8

搞Java装机必备的

### 下载

[https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html](https://link.zhihu.com/?target=https%3A//www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) Ubuntu系统下选择X64的tar包，当然rpm包安装也可。

![img](https://pic3.zhimg.com/80/v2-5d1b538ff588cccc1a6be9fd881b8afa_1440w.jpg)

### 安装

主要就是解压缩包 + 配置环境变量,我习惯将tar包移动到/user/local/下

**a.解压缩**

```bash
sudo tar -xzvf /user/local/software/jdk-8u191-linux-x64.tar.gz
```

配置环境变量，根据自己需求配置用户/系统变量下面以用户变量为例：

**b. 编辑环境变量**

`sudo vim ~/.bashrc` ~/的意思是在当前用户的主目录下，找.bashrc文件等价于/home/user_name/.bashrc

```bash
export JAVA_HOME=/usr/local/jdk1.8.0_191
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=.:${JAVA_HOME}/bin:$PATH
```

**c.刷新变量**

`source ~/.bashrc` 完成后，java -version看到java版本号，即表示安装成功！

## 16.Maven

搞Java当然少不了Maven,二者的关系就行python少不了pip,前端少不了npm：）

### 下载

[https://maven.apache.org/download.cgi](https://link.zhihu.com/?target=https%3A//maven.apache.org/download.cgi)

![img](https://pic4.zhimg.com/80/v2-80851a9dcdb118c657d443e7c5b765c7_1440w.jpg)

### 安装

和安装Java一样，很简单，只不过多了一个配置镜像源的步骤。

**a.解压缩**

```bash
sudo tar -xzvf /user/local/apache-maven-3.6.2-bin.tar.gz
```

**b.编辑环境变量**

```
sudo vim ~/.bashrc
export MAVEN_HOME=/usr/local/apache-maven-3.6.2
export PATH=${MAVEN_HOME}/bin:$PATH
```

**c.刷新变量**

```
source ~/.bashrc
```

### 配置镜像源

由于maven镜像在国外，由于大家都知道的原因，直接用默认源下载资源是很慢的，需要换成国内的镜像源头，可以直接配阿里源： 编辑maven的settings.xml文件（maven主目录下/conf/），在区块之间加入：

```xml
<mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

如果需要添加其他代理仓库，可参考：[官方指南](https://link.zhihu.com/?target=https%3A//help.aliyun.com/document_detail/102512.html%3Fspm%3Da2c40.aliyun_maven_repo.0.0.36183054D18M8i)

## 17.Postman

Web开发必备的神器

### 下载+安装

千万不要费劲，照着百度到的一系列的安装教程来安装，直接在Ubuntu自带的【Ubuntu软件】中搜索Postman，直接傻瓜式安装即可

![img](https://pic4.zhimg.com/80/v2-c225f88a5ccfae26e62b3d53dd2eb0db_1440w.jpg)

## 18.IntelliJ IDEA

jetbrains公司出品的，宇宙第一好用的Java IDE，谁用谁知道

### 下载

[http://www.jetbrains.com/idea/download/#section=linux](https://link.zhihu.com/?target=http%3A//www.jetbrains.com/idea/download/%23section%3Dlinux)

![img](https://pic2.zhimg.com/80/v2-b1466fe271bf9931a0cca218251772a9_1440w.jpg)

直接下载Ultimate版，官网很温馨地提示了，可以使用支付宝付款，有实力的还是支持正版，实在不行淘宝上买一个激活码即可：）

### 安装

将下载好的安装包，放在你需要的位置，譬如：/user/local/software/

```bash
cd /user/local/software/
# 解压缩
sudo tar -xzvf ideaIU-2019.2.4.tar.gz
# 解压完的文件夹：idea-IU-192.7142.36
# 更改权限
sudo chmod 755  idea-IU-192.7142.36
#执行安装脚本
sh idea-IU-192.7142.36/bin/idea.sh
```

## 19.Pycharm

和IDEA师出同门，是非常好用的一款Python IDE,有钱请支付宝支持一波，否则，还是用激活码吧，对了Jetbrains系列的软件可以公用一个激活码哦：）

### 下载

[http://www.jetbrains.com/pycharm/download/#section=linux](https://link.zhihu.com/?target=http%3A//www.jetbrains.com/pycharm/download/%23section%3Dlinux)

![img](https://pic1.zhimg.com/80/v2-0992c726c77b68a4431d19c03d984cb8_1440w.jpg)

### 安装

同IDEA，也是直接解压缩，cd到主目录/bin,执行`sh ./pycharm.sh`

## 20.Anaconda

Anaconda是用来管理各种虚拟环境和包的，搞AI必用的一款软件，官网直接找对应的系统下载即可。

### 下载

[https://www.anaconda.com/distribution/#download-section](https://link.zhihu.com/?target=https%3A//www.anaconda.com/distribution/%23download-section)

### 安装

安装比较简单，切换到root用户执行或者sudo执行： `bash /your_path_to/Anaconda3-2019.10-Linux-x86_64.sh` 根据提示输入Enter,yes即可，最后会确认路径，如果用默认的直接Enter否则输入自定义的安装路径再按Enter即可。安装完成后 `conda --version` 能看到版本号即表示安装成功 安装完成，根据自己需要配置环境变量：

```bash
export CONDA_PATH=/usr/local/software/anaconda3
export PATH=${CONDA_PATH}/bin:$PATH
```

> **注意：**如果在安装过程中没有出现初始化选项，可以使用命令`conda init zsh`

### 卸载

删除anaconda，直接删除文件夹+清理环境变量即可

**a.删除主文件夹anaconda3**

直接找到安装时的anaconda3文件夹即可，可以用： `sudo find / -type d -name anaconda3`找到文件夹 然后删除文件夹`sudo rm -rf /your_path_to/anaconda3`

**b.删除文件夹**

删除anaconda的配置文件夹.condarc，可以用命令： `sudo find / -type f -name .condarc`找到其安装位置，删除之。 删除环境包文件夹.conda，命令同上。

**c.删除conda初始化脚本**

通常conda会在.bashrc中创建一段脚本，如下：

![img](https://pic4.zhimg.com/80/v2-4110d7944f3ccdc6281e47d7b90a1887_1440w.jpg)

如果是root管理员默认位置安装，则该脚本位于/root/.bashrc；如果是普通用户安装，则通常位于/home/your_user_name/.bashrc。譬如我的.bashrc位于/home/lyon/下，执行: vim /home/lyon/.bashrc，删除这段conda initialize初始化脚本

**d.清除环境变量**

需要注意的是，如果你配置了anaconda的环境变量，则需要在对应的bashrc或profile中删除掉。如果你配置的用户变量，通常在/home/your_user_name/下可以找到.bashrc和.profile，如果是系统变量，则通常是/etc/profile

## 21.MySQL8.0

据说mysql8.0相比于5.7有了不小的升级，于是决定安个新版8.0试试，传统的mysql安装还是比较麻烦的，这里推荐直接用官网给出的APT安装方式，适合Ubuntu、Debian系统

### 下载

[https://dev.mysql.com/downloads/repo/apt/](https://link.zhihu.com/?target=https%3A//dev.mysql.com/downloads/repo/apt/) 首先下载mysql配置工具，后面的配置都通过它来完成

![img](https://pic1.zhimg.com/80/v2-ac643477d09117cd0051f786e210531c_1440w.jpg)

### 安装

同样，将下载好的文件放在适合的位置，譬如/user/local/

```bash
cd /usr/local
sudo dpkg -i mysql-apt-config_0.8.14-1_all.deb
# 安装mysql-apt-config时会让你选择需要安装的版本，之后继续：
sudo apt-get update
sudo apt-get install mysql-server
```

安装完成后，服务自动，可以用mysql --version查看版本号：

```bash
> mysql --version
mysql  Ver 8.0.18 for Linux on x86_64 (MySQL Community Server - GPL)
```

更多说明详见：[MySQL APT存储库的快速指南](https://link.zhihu.com/?target=https%3A//dev.mysql.com/doc/mysql-apt-repo-quick-guide/en/%23repo-qg-apt-select-series) [mysql-apt-repo-quick-guide-en.pdf](https://link.zhihu.com/?target=https%3A//www.yuque.com/attachments/yuque/0/2019/pdf/216914/1572936703086-b50821f8-0ecb-4639-9e75-23714a28847f.pdf%3F_lake_card%3D%7B%22uid%22%3A%221572936699881-0%22%2C%22src%22%3A%22https%3A%2F%2Fwww.yuque.com%2Fattachments%2Fyuque%2F0%2F2019%2Fpdf%2F216914%2F1572936703086-b50821f8-0ecb-4639-9e75-23714a28847f.pdf%22%2C%22name%22%3A%22mysql-apt-repo-quick-guide-en.pdf%22%2C%22size%22%3A66846%2C%22type%22%3A%22application%2Fpdf%22%2C%22ext%22%3A%22pdf%22%2C%22progress%22%3A%7B%22percent%22%3A0%7D%2C%22status%22%3A%22done%22%2C%22percent%22%3A0%2C%22id%22%3A%22u6kyG%22%2C%22card%22%3A%22file%22%7D)

### 命令

- **查看状态：sudo service mysql status**
- **启动服务：sudo service mysql start**
- **停止服务：sudo service mysql stop**

### 22.Navicat

### 下载

[Navicat Premium 14 天免费 Windows、macOS 和 Linux 的试用版](https://link.zhihu.com/?target=https%3A//www.navicat.com.cn/download/navicat-premium)

![img](https://pic2.zhimg.com/80/v2-f764cee8aea43af5b2fc213ff3f9cec9_1440w.jpg)

### 安装

下载好的tar包解压到合适位置，我这里是/usr/local/software/navicat121_premium_cs_x64

cd到解压后的主文件夹

```bash
cd /usr/local/software/navicat121_premium_cs_x64
```

vim修改启动脚本，改文字从en_US.UTF-8改为中文zh_CN.UTF-8，否则文字显示会有问题

```text
sudo vim start_navicat
```

![img](https://pic2.zhimg.com/80/v2-06da41a09d84bedb67a22bcefaec78f1_1440w.jpg)

改完以后,执行脚本：

```text
./start_navicat
```

脚步执行会下载wine（一个Navicat运行的虚拟环境）过程会比较慢，大约5分钟～启动Navicat的界面后，会发现文字虽然是中文，不过还是有缺失现象，需要改一下字体设置：

```text
工具>>选项>>常规>>界面,字体更改为：Noto Sans Mono CJK SC Regular
```

实际测试发现，对mysql5.X版本可以连上，对新版的mysql8.0无法连接，会自动退出

### 激活

下载完成后，是默认14天的试用期，可以通过如下方式激活：[https://blog.csdn.net/qq_25135655/article/details/89843202](https://link.zhihu.com/?target=https%3A//blog.csdn.net/qq_25135655/article/details/89843202)

### 连接MySQL8.0

实测发现连接mysql5.6可以正常连接，连mysql8.0则navicat会报错，原来mysql8.0修改了密码的过期方式，默认为90天过期，需要进行如下设置：

```text
# 进入mysql控制台
mysql -u root -p
# 更改密码过期方式为NEVER（永不过期）
mysql > ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;
# 重置root用户初始密码
mysql > ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
# 刷新
mysql > FLUSH PRIVILEGES;
```

![img](https://pic2.zhimg.com/80/v2-e176a3ec85a3ac42c86d30f22662ba01_1440w.jpg)

改完以后，用navicat就可以正常连接了