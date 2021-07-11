# 项目部署

## 打jar包

在idea maven 插件里面，先**clean**，后**package**命令

打包后的jar，在**target**文件夹下，使用以下命令运行

```bash
java -jar my
```

## springboot 打包成jar包的时候发布到云平台上面运行如何保持后台长期运行（关掉xShell仍然运行）

> 1. 新建一个脚本比如名字叫start.sh 里面的内容如下(xxxxx.jar 是要运行的jar包):
>
> `java -jar xxxxx.jar`
>
> 2. 然后退出保存，修改start.sh脚本权限 :`chmod 777 start.sh`
>
> 3. `nohup ./start.sh > /dev/null 2> /dev/null &`

