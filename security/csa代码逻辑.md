# CSA代码逻辑

> 主要逻辑：使用python实现算法。在Java后端使用`python xxx.py param`形式的命令执行python代码，并将python运行结果，通过`print()`方法输入到输入流，Java那边通过从输入流取出数据。
>
> 问题排查：
>
> 1. 在命令行终端环境,运行出问题的API接口所调用的Python文件，一般`python xxx.py param`，参数通过`sys.argv`数组获取。输入结果必须按照指定格式，**不能有多余输出**
> 2. python输出格式，**结果使用空格分隔，一般有多行**
> 3. 排除python问题，debug检查调用参数是否正确，及拼接的命令行是否有误，`application.yml`路径是否配置有误。
> 4. 由于本地环境没有python，所以联调较为困难，使用上述方法，仍无法解决，考虑使用idea远程运行项目，在服务器上进行联调。
>
> 添加功能：
>
> 1. python完成算法，确定输入参数，通过`sys.argv[]`获取输入参数
> 2. 在`application.yml`中配置python文件路径
> 3. Java调用python可参考`utils.PythonUtil.java`中的写法，类似Scanner读取数据
> 4. `constant`包中有很多常量，比如：关键节点方法名中英转化，redis存储数据的键名等
> 5. 如果要使用redis的话，建议实现一个对应的CacheDao
> 6. 一般可以从service层开始做业务逻辑，利用util类读取python返回的数据，如何封装需和前端对齐。
> 7. 返回前端的响应一般都有对象对数据进行了封装，但是后来接的太繁琐，直接使用Map，非常方便，一般不建议。