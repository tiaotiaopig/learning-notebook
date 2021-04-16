# ubuntu18.04 快捷方式

## 基本知识

> * ubuntu18.04的应用通常没有快捷方式,很多是使用 `*.sh`文件启动
> * 我们需要自己创建快捷方式,快捷方式文件命名:`filename.desktop`
> * 快捷方式的存储位置:`/usr/share/applications`(全局),`~/.local/share/applications`(个人)
> * 存储在个人位置的快捷方式,安装位置通常在`~/.local/share/`

---

## 基本模板

```Shell
[Desktop Entry]
Version=1.0
Type=Application
Name=IntelliJ IDEA
Icon=/usr/local/develop/idea-IU-202.7319.50/bin/idea.svg
Exec=/usr/local/develop/idea-IU-202.7319.50/bin/idea.sh
Comment=Capable and Ergonomic IDE for JVM
Categories=Development;IDE;
Terminal=false
StartupWMClass=jetbrains-idea
```

## 双图标问题

1. 打开多图标的应用

 	2. 终端输入 `xprop |grep WM_CLASS` ,运行后，光标变成了+号，移动+号到已经打开的程序窗口内，点击。终端便有了桌面应用信息
 	3. 复制第二个字符串,作为`StartupWMClass` 的值

## 网易云字体大小

<<<<<<< HEAD
> `Exec=netease-cloud-music --force-device-scale-factor=1.5 %U`
=======
> `Exec=netease-cloud-music --force-device-scale-factor=1.5 %U`

##  AppImage的解包和打包

> `./NAME.AppImage --appimage-extract` 解包
>
> `./appimagetool-x86_64.AppImage squashfs-root NAME_NEW.AppImage` 打包

>>>>>>> 611855de785fb01f3af81c6a1d22a15cac841838
