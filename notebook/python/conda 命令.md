# conda 命令

## 获取帮助

```bash
conda -h
```

获取帮助(某些命令下的帮助)

```bash
如
conda env --help
```

## 查看版本

查看conda版本

```undefined
conda --version
conda -V
```

检查当前环境的python版本

```undefined
python --version
python -V
```

## 包管理

查看已安装的包(当前活跃环境下)

```cpp
conda list
```

查看已安装的包(非当前活跃环境下)

```cpp
conda list -n 环境名称
```

使用conda命令安装包(当前激活环境)

```undefined
conda install 包名

或者使用

pip install 包名
```

使用conda命令安装包(当前激活环境, 安装特定版本)

```undefined
conda install 包名=版本

或者使用

pip install 包名==版本
```

使用conda命令安装包(为指定环境, 安装特定版本)

```undefined
conda install -n 环境名称 包名=版本

或者使用

pip install -n 环境名称 包名==版本
```

搜索包版本

```undefined
conda search 包名
```

## 环境管理

创建指定名称环境

```undefined
conda create --name 环境名称
```

创建指定名称环境(并指定python版本)

```undefined
conda create --name 环境名称 python=指定版本号
```

创建指定名称环境(并包含某些包)

```undefined
conda create --name 环境名称 numpy scipy
```

创建指定名称环境(指定python版本下并包含某些包的环境)

```undefined
conda create --name 环境名称 python=python版本 numpy scipy
```

列举创建过的所有环境

```cpp
conda env list
```

切换到另一个环境

```bash
conda activate XXX
```

删除某个环境

```csharp
conda remove --name 环境名称 --all
```

导出环境yml（使用conda 安装的包）

```bash
导出到当前工作目录
conda env export > xxx.yml
导出到指定目录
conda env export > /home/user/xxx.yml
```

导入yml创建环境（使用conda 安装的包）

```bash
从当前工作目录导入
conda env create -f xxx.yml
从指定目录导入
conda env create -f /home/user/xxx.yml
```

导出 **requirements.txt** (使用pip安装的包)

```
 pip freeze > requirements.txt
```

pip导入**requirements.txt**中列出的库到系统(使用pip安装的包)

```
 pip install -r requirements.txt
```

## 镜像相关

添加镜像源

```csharp
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
```

换回默认镜像

```csharp
conda config --remove-key channels
```

查看源

```dart
conda config --show-sources
```

设置搜索时显示通道地址

```bash
conda config --set show_channel_urls yes
```

将会显示conda的配置信息

```dart
conda config --show
```