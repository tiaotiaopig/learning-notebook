# ubuntu18.04 + windows10双系统使用rEFInd引导(安装rEFInd-minimal主题)

---

## 安装rEFInd

* 命令行安装:

    ```powershell
    $ sudo apt-add-repository ppa:rodsmith/refind
    $ sudo apt-get update
    $ sudo apt-get install refind
    ```

* deb包安装

    > http://www.rodsbooks.com/refind/installing.html#uinst_linux

## 下载Minimalistic rEFInd theme

> 1. 进入到`/boot/efi/EFI/refind/`
> 2. 创建`themes`文件夹
> 3. 将主题git克隆到`themes`文件下
> 4. 在`refind.conf`的末尾添加`include themes/rEFInd-minimal/theme.conf`

具体命令如下:

```shell
sudo -s
cd /boot/efi/EFI/refind
mkdir themes
git clone https://github.com/EvanPurkhiser/rEFInd-minimal.git
```

## 配置`refind.conf`(末尾添加)

> `include themes/rEFInd-minimal/theme.conf
> resolution 1920 1080
> dont_scan_dirs \efi\boot
> scan_all_linux_kernels false`

说明:修改分辨率,忽略了一些不必要的加载项

​	启动项会自动检测,不必按照文件上说的手动添加

## 调整boot启动顺序

> 刚开始在Windows端使用easyUEFI调整的,但是修改不生效(浪费我时间)
>
> 直接进BIOS进行调整,简单粗暴

## 隐藏Linux的grub引导

> 完成上面四步,引导项就已经使用了rEFInd-minimal主题
>
> 但是进入ubuntu时仍然会有grub引导(多此一举),所以我们把它隐藏

修改Ubuntu默认引导文件

```shell
sudo vim /etc/default/grub
```

修改内容如下

```shell
GRUB_DEFAULT=0
GRUB_HIDDEN_TIMEOUT=0
GRUB_HIDDEN_TIMEOUT_QUIET=true
GRUB_TIMEOUT=0
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""
GRUB_DISABLE_OS_PROBER=true
```

主要是:`GRUB_TIMEOUT=0`,`GRUB_DISABLE_OS_PROBER=true`

更新grub

```shell
 sudo update-grub
```

## 卸载

进入BIOS修改boot启动顺序(很重要)

进ubuntu卸载rEFInd

```shell
sudo rm -r /boot/efi/EFI/refind
```

恢复Linux的grub引导

```Shell
sudo vim /etc/default/grub
```

调整``GRUB_TIMEOUT=20``(时间你自己定)

删除`GRUB_DISABLE_OS_PROBER=true`

更新grub

```shell
sudo update-grub
```