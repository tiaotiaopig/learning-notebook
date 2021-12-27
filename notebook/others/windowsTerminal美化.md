# Windows Terminal 美化

## 安装 Powerline 字体

>  Powerline 使用字形来设置提示符样式。 如果你的字体不包含 Powerline 字形，则在整个提示符中，你可能会看到若干 Unicode 替换字符“&#x25AF”。 尽管 [Cascadia Mono](https://docs.microsoft.com/zh-cn/windows/terminal/cascadia-code) 不包括 Powerline 字形，但你可以安装 Cascadia Code PL 或 Cascadia Mono PL，这两者包含 Powerline 字形。 可以从 [Cascadia Code GitHub 发布页](https://github.com/microsoft/cascadia-code/releases)安装这些字体。

## 在 PowerShell 中设置 Powerline

### PowerShell 必备条件

如果尚未安装，请 [安装适用于 Windows 的 Git](https://git-scm.com/downloads)。

使用 PowerShell，安装 Posh-Git 和 Oh-My-Posh：

PowerShell复制

```powershell
Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser
```

 提示

如果尚未安装 NuGet，可能需要安装它。 如果是这种情况，PowerShell 命令行会询问是否要安装 NuGet。 选择 [Y]“是”。 你可能还需要批准从不受信任的存储库 [PSGallery](https://docs.microsoft.com/zh-cn/powershell/scripting/gallery/getting-started?view=powershell-7) 中安装模块。 选择 [Y]“是”。

[Posh-Git](https://github.com/dahlbyk/posh-git) 将 Git 状态信息添加到提示，并为 Git 命令、参数、远程和分支名称添加 tab 自动补全。 [Oh-My-Posh](https://github.com/JanDeDobbeleer/oh-my-posh) 为 PowerShell 提示符提供主题功能。

如果使用的是 PowerShell Core，请安装 PSReadline：

PowerShell复制

```powershell
Install-Module -Name PSReadLine -Scope CurrentUser -Force -SkipPublisherCheck
```

[PSReadline](https://docs.microsoft.com/zh-cn/powershell/module/psreadline/?view=powershell-6) 允许在 PowerShell 中自定义命令行编辑环境。

### 自定义 PowerShell 提示符

使用 `notepad $PROFILE` 或所选的文本编辑器打开 PowerShell 配置文件。 这不是你的 Windows 终端配置文件。 你的 PowerShell 配置文件是一个脚本，该脚本在每次启动 PowerShell 时运行。 [详细了解 PowerShell 配置文件](https://docs.microsoft.com/zh-cn/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7)。

在 PowerShell 配置文件中，将以下内容添加到文件的末尾：

PowerShell复制

```powershell
Import-Module posh-git
Import-Module oh-my-posh
Set-Theme Paradox
```

现在，每个新实例启动时都会导入 Posh-Git 和 Oh-My-Posh，然后从 Oh-My-Posh 设置 Paradox 主题。 Oh-My-Posh 附带了若干[内置主题](https://github.com/JanDeDobbeleer/oh-my-posh#themes)。

> **这里有坑**，Oh-My-Posh好像更新啦，最后一个命令没有啦
>
> 官方是使用 **Get-PoshThemes** 查看支持主题；**Set-PoshPrompt -Theme jandedobbeleer** 设置主题
>
> 这两个命令可以直接在shell中运行

### 在设置中将 Cascadia Code PL 设置为 fontFace

若要设置 Cascadia Code PL 以便与 PowerLine 一起使用（在系统中下载、解压缩和安装之后），需要通过从“Windows 终端”下拉菜单中选择“设置”（Ctrl+，）来打开 settings.json 文件中的[配置文件设置](https://docs.microsoft.com/zh-cn/windows/terminal/customize-settings/profile-settings)。

settings.json 文件打开后，找到 Windows PowerShell 配置文件，并添加 `"fontFace": "Cascadia Code PL"`，以便将 Cascadia Code PL 指定为字体。 这样就会显示很好看的 Cascadia Code Powerline 字形。 在编辑器中选择“保存”后，终端应会即刻显示出变化。

Windows PowerShell 配置文件 settings.json 文件现在应如下所示：

JSON复制

```json
{
    // Make changes here to the powershell.exe profile.
    "guid": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
    "name": "Windows PowerShell",
    "commandline": "powershell.exe",
    "fontFace": "Cascadia Code PL",
    "hidden": false
},
```

## powershell alias

其实就是写一个powershell函数

```powershell
# 查看powershell的配置文件
echo $PROFILE
# 这是我的配置位置
cd C:\Users\28072\Documents\PowerShell\Microsoft.PowerShell_profile.ps1
# 使用vscode打开，添加一个函数
function login {
    ssh lifeng@192.168.50.17
}
# 执行设置
Set-ExecutionPolicy RemoteSigned # 这个需要管理员身份运行
# 或者为当前用户生效
Set-ExecutionPolicy -Scope CurrentUser
# 然后输入RemoteSigned即可
```

