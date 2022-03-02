# Windows Terminal 终极配置指南

最终效果：

[![image-20210227185710990](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/image-20210227185710990.png)](https://sunkai-markdown-pics.oss-cn-shanghai.aliyuncs.com/imgs/image-20210227185710990.png)

## 一、安装 Windows Terminal

直接在 Microsoft Store 安装 [Windows Terminal](https://aka.ms/terminal)：

[![image-20210222153625100](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/image-20210222153625100.png)](https://sunkai-markdown-pics.oss-cn-shanghai.aliyuncs.com/imgs/image-20210222153625100.png)

安装完成后的默认界面如图：

[![image-20210227205802637](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/image-20210227205802637.png)](https://sunkai-markdown-pics.oss-cn-shanghai.aliyuncs.com/imgs/image-20210227205802637.png)

## 二、安装 Scoop

[Scoop](https://github.com/lukesampson/scoop) 是 Windows 上非常好用的包管理工具，包含很多常用的开发工具。

### 步骤 1 - 允许 Power Shell 安装第三方包：

```
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

### 步骤 2 - 安装 Scoop：

```
Invoke-Expression (New-Object System.Net.WebClient).DownloadString('https://get.scoop.sh')
 
# or shorter
iwr -useb get.scoop.sh | iex
```

### 步骤 3 - 添加常用软件源：

```
# 由于某些原因不能加入主软件源的附加源
scoop bucket add 'extras'
 
# NF 字体源
scoop bucket add 'nerd-fonts'
 
# Scoop 自动补全源
scoop bucket add scoop-completion https://github.com/Moeologist/scoop-completion
```

### 步骤 4 - 安装常用软件和字体

```
# 以管理员权限执行命令
scoop install sudo
 
# 加速下载
scoop install aria2
 
# 自动补全
scoop install scoop-completion
 
# 常用字体
sudo scoop install Firacode
sudo scoop install Meslo-NF  # 必须安装
sudo scoop install Font-Awesome
sudo scoop install Source-Han-Serif-SC
```

### 步骤 5 - 检查 Scoop 配置并按照指示修改

```
# 检查
scoop checkup
 
# 必要软件
scoop install 7zip
scoop install innounp
scoop install dark
 
# 设置长路径
sudo Set-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem' -Name 'LongPathsEnabled' -Value 1
```

### Scoop 常用命令

从上面的命令中，我们可以发现 Scoop 命令的设计很简单（和 Homebrew 等 Unix-style 的工具一样），是「`scoop` + 动作 + 对象」的语法。其中「对象」是可省略的。

[![img](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/69f2db0e372074441ceb4f2fcf96d31d.png)](https://cdn.sspai.com/2019/01/11/69f2db0e372074441ceb4f2fcf96d31d.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

最常用的几个基础动作有这些：

|   命令    |     动作     |    对象    |
| :-------: | :----------: | :--------: |
|  🌟status  | 查看软件状态 |            |
|  🌟search  |  搜索软件名  |   软件名   |
| 🌟install  |   安装软件   |   软件名   |
|  update   |   更新软件   | 软件名或 * |
| uninstall |   卸载软件   |   软件名   |
|   info    | 查看软件详情 |   软件名   |
|   home    | 打开软件主页 |   软件名   |
|   help    |   查看帮助   |    命令    |

```
# 更新所有软件
scoop update *
 
# 卸载 scoop
scoop uninstall scoop
 
# 清除缓存
scoop cache rm *
 
# 清除软件其他版本
scoop cleanup *
```

## 三、安装 WSL

按照[适用于 Linux 的 Windows 子系统安装指南 (Windows 10)](https://docs.microsoft.com/zh-cn/windows/wsl/install-win10)的步骤安装 WSL：

### 步骤 1 - 启用适用于 Linux 的 Windows 子系统

```
sudo dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

### 步骤 2 - 检查运行 WSL 2 的要求

- 对于 x64 系统：**版本 1903** 或更高版本，采用 **内部版本 18362** 或更高版本。
- 对于 ARM64 系统：**版本 2004** 或更高版本，采用 **内部版本 19041** 或更高版本。
- 低于 18362 的版本不支持 WSL 2。 使用 [Windows Update 助手](https://www.microsoft.com/software-download/windows10)更新 Windows 版本。

若要检查 Windows 版本及内部版本号，选择 Windows 徽标键 + R，然后键入“winver”，选择“确定”。 （或者在 Windows 命令提示符下输入 `ver` 命令）。 更新到“设置”菜单中的[最新 Windows 版本](ms-settings:windowsupdate)。

### 步骤 3 - 启用虚拟机功能

```
sudo dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

### 步骤 4 - 下载 Linux 内核更新包

- [适用于 x64 计算机的 WSL2 Linux 内核更新包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

### 步骤 5 - 将 WSL 2 设置为默认版本

```
wsl --set-default-version 2
```

### 步骤 6 - 安装所选的 Linux 分发

1. 打开 [Microsoft Store](https://aka.ms/wslstore)，并选择你偏好的 Linux 分发版。

[![Microsoft Store 中的 Linux 分发版的视图](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/store.png)](https://docs.microsoft.com/zh-cn/windows/wsl/media/store.png)

单击以下链接会打开每个分发版的 Microsoft Store 页面：

- [Ubuntu 16.04 LTS](https://www.microsoft.com/store/apps/9pjn388hp8c9)
- [Ubuntu 18.04 LTS](https://www.microsoft.com/store/apps/9N9TNGVNDL3Q)
- [Ubuntu 20.04 LTS](https://www.microsoft.com/store/apps/9n6svws3rx71)
- [openSUSE Leap 15.1](https://www.microsoft.com/store/apps/9NJFZK00FGKV)
- [SUSE Linux Enterprise Server 12 SP5](https://www.microsoft.com/store/apps/9MZ3D1TRP8T1)
- [SUSE Linux Enterprise Server 15 SP1](https://www.microsoft.com/store/apps/9PN498VPMF3Z)
- [Kali Linux](https://www.microsoft.com/store/apps/9PKR34TNCV07)
- [Debian GNU/Linux](https://www.microsoft.com/store/apps/9MSVKQC78PK6)
- [Fedora Remix for WSL](https://www.microsoft.com/store/apps/9n6gdm4k2hnc)
- [Pengwin](https://www.microsoft.com/store/apps/9NV1GV1PXZ6P)
- [Pengwin Enterprise](https://www.microsoft.com/store/apps/9N8LP0X93VCP)
- [Alpine WSL](https://www.microsoft.com/store/apps/9p804crf0395)

1. 在分发版的页面中，选择“获取”。

[![Microsoft Store 中的 Linux 分发版](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/ubuntustore.png)](https://docs.microsoft.com/zh-cn/windows/wsl/media/ubuntustore.png)

首次启动新安装的 Linux 分发版时，将打开一个控制台窗口，系统会要求你等待一分钟或两分钟，以便文件解压缩并存储到电脑上。 未来的所有启动时间应不到一秒。

然后，需要[为新的 Linux 分发版创建用户帐户和密码](https://docs.microsoft.com/zh-cn/windows/wsl/user-support)。

[![Windows 控制台中的 Ubuntu 解包](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/ubuntuinstall.png)](https://docs.microsoft.com/zh-cn/windows/wsl/media/ubuntuinstall.png)

**祝贺你！现已成功安装并设置了与 Windows 操作系统完全集成的 Linux 分发！**

## 四、美化 Power Shell

美化 Power Shell 需要安装以下三个包：

- [oh-my-posh](https://ohmyposh.dev/)：Shell 主题引擎。
- [posh-git](https://github.com/dahlbyk/posh-git/)：Power Shell 下提供 Git 增强。
- [Terminal-Icons](https://github.com/devblackops/Terminal-Icons)：文件（夹）图标。

```
# 安装相关模块
Install-Module oh-my-posh -Scope CurrentUser
Install-Module posh-git -Scope CurrentUser -AllowClobber
Install-Module Terminal-Icons -Scope CurrentUser
 
# 如需卸载，命令如下
sudo Uninstall-Module -Name oh-my-posh -AllVersions -Force
sudo Uninstall-Module -Name posh-git -AllVersions -Force
sudo Uninstall-Module -Name Terminal-Icons -AllVersions -Force
```

美化后的效果如下：

[![image-20210224224724669](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/image-20210224224724669.png)](https://sunkai-markdown-pics.oss-cn-shanghai.aliyuncs.com/imgs/image-20210224224724669.png)

## 五、配置 Power Shell

打开 Windows Terminal，默认会启动 Power Shell，输入以下代码如果有配置文件会打开，没有会新建：

```
if (!(Test-Path -Path $PROFILE )) { New-Item -Type File -Path $PROFILE -Force }
notepad $PROFILE
```

在配置文件中粘贴以下代码：

```
# Import Modules
Import-Module oh-my-posh
Import-Module posh-git
Import-Module Terminal-Icons
Import-Module "$($(Get-Item $(Get-Command scoop).Path).Directory.Parent.FullName)\modules\scoop-completion"
 
# Set theme
Set-PoshPrompt -Theme Honukai
 
$env:PYTHONIOENCODING="utf-8"
 
# Functions
function Git-Log {git log --graph --pretty='%Cred%h%Creset -%C(auto)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --all}
function Get-IP {curl -L tool.lu/ip}
function Update-Scoop {scoop update; scoop update *}
function Get-AllHistory
{
    $his = Get-Content (Get-PSReadLineOption).HistorySavePath
    $n = $his.Length
    $out = @()
    for($i=0;$i -lt $n;$i++)
    {
        $out = $out + "$i $($his[$i])"
    }
    return $out
}
function New-ll {Get-ChildItem -Force}
 
# $DefaultUser = 'Kai'
 
# Remove curl alias
If (Test-Path Alias:curl) {Remove-Item Alias:curl}
 
# Remove-Item alias:ls -force
Set-Alias ll New-ll
 
Set-PSReadlineKeyHandler -Key Tab -Function MenuComplete
```

如果需要切换主题，可以使用以下指令：

```
Get-PoshThemes  # 预览主题
Set-PoshPrompt -Theme Honukai  # 设置主题为 Honukai
```

## 六、配置 Windows Terminal

点击 Windows Terminal 的配置选项，会打开一个 json 文件，将以下代码粘贴到其中：

```json
// This file was initially generated by Windows Terminal 1.5.10411.0
// It should still be usable in newer versions, but newer versions might have additional
// settings, help text, or changes that you will not see unless you clear this file
// and let us generate a new one for you.
 
// To view the default settings, hold "alt" while clicking on the "Settings" button.
// For documentation on these settings, see: https://aka.ms/terminal-documentation
{
    "$schema": "https://aka.ms/terminal-profiles-schema",
 
    "defaultProfile": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
 
    // You can add more global application settings here.
    // To learn more about global settings, visit https://aka.ms/terminal-global-settings
 
    // If enabled, selections are automatically copied to your clipboard.
    "copyOnSelect": false,
 
    // If enabled, formatted data is also copied to your clipboard
    "copyFormatting": false,
 
    // A profile specifies a command to execute paired with information about how it should look and feel.
    // Each one of them will appear in the 'New Tab' dropdown,
    //   and can be invoked from the commandline with `wt.exe -p xxx`
    // To learn more about profiles, visit https://aka.ms/terminal-profile-settings
    "profiles": {
        "defaults": {
            // Put settings here that you want to apply to all profiles.
            "closeOnExit": true,
            "fontFace": "MesloLGM NF",  // oh-my-posh 默认字体
            "fontSize": 12,  // 字体大小
            "acrylicOpacity": 0.6,  // 背景透明度 (0-1)
            "useAcrylic": true,  // 启用毛玻璃特效
            "backgroundImage": "C:\\Users\\Kai\\OneDrive\\Pictures\\Wallpapers\\Windows Insider\\WIP-6th-anniversary-wallpaper-dark.jpg",  // 背景图片
            "backgroundImageOpacity": 0.6,  // 背景图片透明图 (0-1)
            // "experimental.retroTerminalEffect": true,  // 复古 CRT 效果
            "antialiasingMode": "cleartype",  // 消除文字锯齿
            "startingDirectory": "."
        },
        "list": [{
                // Make changes here to the powershell.exe profile.
                "guid": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
                "name": "Windows PowerShell",
                "commandline": "powershell.exe",
                "colorScheme": "Solarized Dark Higher Contrast",
                "hidden": false
            },
            {
                // Make changes here to the cmd.exe profile.
                "guid": "{0caa0dad-35be-5f56-a8ff-afceeeaa6101}",
                "name": "命令提示符",
                "commandline": "cmd.exe",
                "hidden": false
            },
            {
                "guid": "{46ca431a-3a87-5fb3-83cd-11ececc031d2}",
                "hidden": false,
                "name": "Kali",
                "source": "Windows.Terminal.Wsl",
                "icon": "C:\\Users\\Kai\\OneDrive\\Pictures\\Icons\\kali.ico"
            },
            {
                "guid": "{2c4de342-38b7-51cf-b940-2309a097f518}",
                "hidden": false,
                "name": "Ubuntu",
                "colorScheme": "Campbell",
                "source": "Windows.Terminal.Wsl",
                "icon": "C:\\Users\\Kai\\OneDrive\\Pictures\\Icons\\ubuntu.ico"
            },
            {
                "guid": "{1c4de342-38b7-51cf-b940-2309a097f589}",
                "hidden": false,
                "name": "Git Bash",
                "colorScheme": "Campbell",
                "commandline": "C:\\Program Files\\Git\\bin\\bash.exe",
                "icon": "C:\\Program Files\\Git\\mingw64\\share\\git\\git-for-windows.ico"
            },
            {
                "guid": "{b453ae62-4e3d-5e58-b989-0a998ec441b8}",
                "hidden": true,
                "name": "Azure Cloud Shell",
                "source": "Windows.Terminal.Azure"
            }
        ]
    },
 
    // Add custom color schemes to this array.
    // To learn more about color schemes, visit https://aka.ms/terminal-color-schemes
    "schemes": [{
        "name": "Solarized Dark Higher Contrast",
        "black": "#002831",
        "red": "#d11c24",
        "green": "#6cbe6c",
        "yellow": "#a57706",
        "blue": "#2176c7",
        "purple": "#c61c6f",
        "cyan": "#259286",
        "white": "#eae3cb",
        "brightBlack": "#006488",
        "brightRed": "#f5163b",
        "brightGreen": "#51ef84",
        "brightYellow": "#b27e28",
        "brightBlue": "#178ec8",
        "brightPurple": "#e24d8e",
        "brightCyan": "#00b39e",
        "brightWhite": "#fcf4dc",
        "background": "#001e27",
        "foreground": "#9cc2c3"
    }],
 
    // Add custom actions and keybindings to this array.
    // To unbind a key combination from your defaults.json, set the command to "unbound".
    // To learn more about actions and keybindings, visit https://aka.ms/terminal-keybindings
    "actions": [
        // Copy and paste are bound to Ctrl+Shift+C and Ctrl+Shift+V in your defaults.json.
        // These two lines additionally bind them to Ctrl+C and Ctrl+V.
        // To learn more about selection, visit https://aka.ms/terminal-selection
        {
            "command": {
                "action": "copy",
                "singleLine": false
            },
            "keys": "ctrl+c"
        },
        {
            "command": "paste",
            "keys": "ctrl+v"
        },
 
        // Press Ctrl+Shift+F to open the search box
        {
            "command": "find",
            "keys": "ctrl+shift+f"
        },
 
        // Press Alt+Shift+D to open a new pane.
        // - "split": "auto" makes this pane open in the direction that provides the most surface area.
        // - "splitMode": "duplicate" makes the new pane use the focused pane's profile.
        // To learn more about panes, visit https://aka.ms/terminal-panes
        {
            "command": {
                "action": "splitPane",
                "split": "auto",
                "splitMode": "duplicate"
            },
            "keys": "alt+shift+d"
        }
    ]
}
```

## 七、右键打开

目前 Windows Terminal 已经支持右键在当前目录打开的功能，但是在 Directory Opus 中还有 Bug，不能右键打开，故需要一个注册表，其内容如下：

```
Windows Registry Editor Version 5.00
 
[HKEY_CLASSES_ROOT\Directory\Background\shell\wt]
@="Windows Terminal Here"
"Icon"="C:\\Users\\Kai\\OneDrive\\Pictures\\Icons\\terminal.ico"
 
[HKEY_CLASSES_ROOT\Directory\Background\shell\wt\command]
@="C:\\Users\\Kai\\AppData\\Local\\Microsoft\\WindowsApps\\wt.exe"
```

## 相关下载