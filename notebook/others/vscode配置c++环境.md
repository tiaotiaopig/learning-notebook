# Visual Studio Code配置C/C++开发环境

## 安装C/C++编译环境

1. 下载MinGW-W64

   下载地址：https://sourceforge.net/projects/mingw-w64/files/?source=navbar
   点击如图链接，进入下载页面。软件为压缩包，解压后，放置非中文目录下，记为INSTALL_PATH

   ![image-20210708180409025](https://i.loli.net/2021/07/08/wRHIGjP1ko3Qzim.png)

   2. 配置环境变量

      在系统环境变量path下增加INSTALL_PATH\bin。
      注：INSTALL_PATH为MinGW安装目录

   3. 验证

      在cmd中分别输入

      g++ -v
      gdb -v
      查看结果，若是输出相关信息，则说明安装成功！

## 安装插件

打开Visual Studio Code，点击左侧操作栏中Extensions，输入c/c++，选择如图插件进行安装

![image-20210708181223792](https://i.loli.net/2021/07/08/chYuvWVrfQpk954.png)

## 项目环境配置

1. 先在项目中创建代码文件，如helloworld.c或helloworld.cpp。注：后续配置的时候会根据文件类型进行配置

2. 配置Build Task

   > 1. **先打开源码文件**(界面要停留在源码文件上，不然下拉框没有这个选项)
   >
   > 2. 选择菜单：Terminal -> Configure Default Build Task…
   >
   > 3. 弹出下拉框
   >
   >    c++程序选择：C/C++:g++.exe build active file
   >
   >    c程序选择：C/C++:gcc.exe build active file
   >
   >    选择后，会在项目路径下生成.vscode/tasks.json文件
   >    注：其中内容基本不用修改

3. 这一步的配置主要是为了将源程序文件生成可执行文件

   选择菜单：Terminal > Run Build Task… ，即可看到左侧文件中多了一个helloworld.exe文件

4. 配置Debug功能

   > 返回源码文件 注：很重要
   >
   > 选择Run > Add Configuration…
   >
   > 出现下拉框，选择C++(GDB/LLDB)
   >
   > 出现下拉框
   >
   > c++程序选择：g++.exe - 生成和调试活动文件
   >
   > c程序选择：gcc.exe - 生成和调试活动文件
   >
   > 选择后，会在项目路径下生成.vscode/launch.json文件
   >
   > 注：其中内容基本不用修改
   >
   > 至此，就可以运行并调试代码了。F5调试查看效果，在下方TERMINAL控制台即可查看输出结果

5. 智能感知配置（可选）

   > Ctrl+Shift+P打开命令面板，选择C/C++: Edit Configurations (UI) ，打开可视化配置界面，同时会生成
   > .vscode/c_cpp_properties.json文件
   >
   > 此配置主要关注编辑器路径，若是之前装过其他编译环境，可能不是之前安装的MinGW-W64，检查若不是的话，下拉更改:
   >
   > c++程序选择：INSTALL_PATH/bin/g++.exe
   > c程序选择：INSTALL_PATH/bin/gcc.exe

## 小结

配置完成后，.vscode会出现四个配置文件

![image-20210708182008521](https://i.loli.net/2021/07/08/W4tbKM6uNIDrjhq.png)



## 引入外部头文件(.h)，如何配置包含路径？

在使用VSCode开发C/C++时，一般除了引入系统包含路径下的头文件，如stdio.h，还会引入自定义或是第三方头文件，比如：

在进行JNI开发时，需要引入jni.h，而此文件在JDK_HOME/include目录下，另外jni.h内引入的jni_md.h，则又在JDK_HOME/include/win32下

默认情况下若是没有进行配置，编辑器IntelliSense会进行错误提示，同时在执行文件是会报如下错误：

fatal error: xxx.h: No such file or directory

那么如何解决上述问题呢?

总共分两步：

1、解决IntelliSense错误提示

在c_cpp_properties.json中，configurations -> includePath节点中，添加路径

如：JAVA_HOME/include，若是需要进行递归扫描路径，则使用JAVA_HOME/include/**
2、解决执行任务时错误

在tasks.json中，tasks->args节点中，添加

"-I",
"JAVA_HOME/include"
若是有多个目录，则如上面重复添加多个即可