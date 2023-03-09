# vscode 配置python环境

## 安装python插件

![image-20211031155750931](https://i.loli.net/2021/10/31/oflE5iQOMuyqkJ8.png)

## 安装python解释器

使用Anaconda和WSL的环境，或者本机安装python

```bash
# 查看python环境是否安装成功
python3 --version
```

## 创建workspace文件夹

使用vscode打开文件夹，或者命令行的方式，**vscode会默认文件夹根目录作为workspace**,这点非常重要

在debug的时候，要配置好路径，才能正常debug

```bash
# 创建项目根目录
mkdir hello
cd hello
# 使用vscode打开当前文件夹
code .
```

打开后，vscode会将这个文件夹作为worksapce，并将当前项目的配置存放在`.vscode/settings.json`

这和全局存储的用户设置是分离的

## 选择python解释器

> 有两种设置方式：
>
> 1. `Ctrl+Shift+P`,搜索`Python: Select Interpreter`
> 2. 打开一个`.py`文件，左下角设置
>
> 注意，如果使用的anaconda，环境名会有前缀，例如：`('base':conda)`

只有创建`.py`结尾的文件，vscode才会识别为python项目，

## 运行

> 1. 右键选择运行
> 2. 右上角运行按钮
> 3. 选择部分代码，右键运行

## debug

点击左边工具栏的小虫子即可，或者运行按钮的下拉框

通常我们需要一个debug配置文件，vscode会提示创建的，选择第一个pyton，会创建一个**launch.json**

![image-20211031162129728](https://i.loli.net/2021/10/31/lfhIaA74XvyujPs.png)

我们可以在**launch.json**配置debug的参数，通用的如下：

```json
{
    "name": "Python: Current File",
    "type": "python",
    "request": "launch",
    "program": "${file}",
    "console": "integratedTerminal",
    "justMyCode": true,
    "cwd": "${fileDirname}",
    "env": {
        "PYTHONPATH": "${workspaceFolder}${pathSeparator}${env:PYTHONPATH}"
    }
}
```

vscode的配置文件中，[一些常用变量的含义](https://code.visualstudio.com/docs/editor/variables-reference)

针对某个python文件的debug，可以在配置中填写命令行参数

```json
{
    "name": "Python: Main.py",
    "type": "python",
    "request": "launch",
    "program": "${workspaceFolder}/Main.py",
    "console": "integratedTerminal",
    "justMyCode": true,
    "args": ["--data-name", "GPA"]
}
```

主要是**"program"**和**"args"**这两项配置

## 跨文件夹引用

我们在跨文件引用时，会报 **module not found** 错误，这是因为vscode未将当前根路径加入python库默认路径

```python
# 查看默认路径
import sys
print(sys.path)
```

解决方案：

1. ```python
   import sys
   sys.path.append(xxx)
   from xxx import func
   ```

2. 修改vscode的**setting.json**,快捷键`ctrl + ,`打开setting，vscode有一套自己的默认配置，只有我们修改默认配置项时，才会在`.vscode`文件夹中生成**setting.json**文件，只需要在该文件中加入一项：

   ```json
   {
       // 将项目根目录加入python路径，解决跨文件夹导入模块导致的‘No module’
       "terminal.integrated.env.linux": {"PYTHONPATH": "${workspaceFolder}"}
   }
   ```

   [stackoverflow解决方案](https://stackoverflow.com/questions/53653083/how-to-correctly-set-pythonpath-for-visual-studio-code)

   [CSDN解决方案](https://dalewushuang.blog.csdn.net/article/details/119661991?spm=1001.2101.3001.6650.3&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-3-119661991-blog-125376794.pc_relevant_recovery_v2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-3-119661991-blog-125376794.pc_relevant_recovery_v2&utm_relevant_index=6)

## 官方说明

[vscode的python插件配置](https://code.visualstudio.com/docs/python/environments)

上面链接有说明，简述如下：

> 1. 环境变量文件默认定义为：`python.envFile:${workspaceFolder}/.env`，可以`ctrl + ,`搜索`python.envFile`进行设置；
> 2. 环境变量都是键值对表示`environment_variable=value`，使用`#`注释，值的部分可以引用**系统已定义**或**之前文件定义**的环境变量，通过`${environment_variable}`引用；
> 3. 环境变量分隔，windows使用分号;作为分隔符，linux使用冒号:作为分隔符；
> 4. debug的配置文件`launch.json`可以配置属性`envFile`，默认参照`${workspaceFolder}/.env`，也可手动设置路径，优先级较高
> 5. `launch.json`的`env`属性优先级最高
> 6. `PYTHONPATH`环境变量可以为python编译器在寻找模块时指定额外路径，在VScode中可以通过`terminal.integrated.env.*`设置以及`.env`文件中设置。前者只在使用命令行的所有位置生效，`linter or formatter`不生效
