# 国内访问Github

## Ubuntu(通过修改hosts文件进行加速)

> 1. 获取域名的解析记录(访问下面两条链接获取)
>
>     * [github.global.ssl.fastly.net](**https://fastly.net.ipaddress.com/github.global.ssl.fastly.net#ipinfo**)
>     * [github.com](https://github.com.ipaddress.com/#ipinfo)
>
>     分别获取两条解析记录(我写的时候地址如下)
>
>     + 199.232.69.194  github.global.ssl.fastly.net
>     + 140.82.113.4    github.com
>
> 2. 修改**hosts**文件
>
>     ```shell
>     sudo vim /etc/hosts
>     ```
>
>     添加以上获取的两条记录
>
> 3. 重启网络 
>
>     ```shell
>     sudo /etc/init.d/networking restart
>     ```
>
>      PS: /etc 下面有一个host和一个hosts，我们要修改的是hosts 一定要注意。

## Windows(修改hosts文件)

手动把cdn和ip地址绑定。

第一步：

获取 github 的 global.ssl.fastly 

地址访问：

http://github.global.ssl.fastly.net.ipaddress.com/#ipinfo

获取cdn和ip域名：

![图片](https://mmbiz.qpic.cn/mmbiz_png/uDRkMWLia28haX0xkib9swiaOnaWvkAbkdPTRmpxBW93ib3qTRE2yXutsewPMbeibqK8DaYMtJcWHt14ppxGWr8q7Tw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

得到：

199.232.69.194 https://github.global.ssl.fastly.net

第二步：获取github.com地址

访问：

https://github.com.ipaddress.com/#ipinfo 

获取cdn和ip：

![图片](https://mmbiz.qpic.cn/mmbiz_png/uDRkMWLia28haX0xkib9swiaOnaWvkAbkdP9Wb2JBg99rTUaxTa51JrGtooyETMxzA847Ev7fGAI6gYuGHwquIdMA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

得到：

140.82.114.4 http://github.com

第三步：修改 host 文件映射上面查找到的 IP

windows系统：

1、修改C:\Windows\System32\drivers\etc\hosts文件的权限，指定可写入：右击->hosts->属性->安全->编辑->点击Users->在Users的权限“写入”后面打勾。如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/uDRkMWLia28haX0xkib9swiaOnaWvkAbkdPoD84PibVLDPTnRFfKpQAZuhK5AG3Hic3U4c2nkAcMzn6WAdsOFZnppPA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

然后点击确定。

注意:还要把 **只读** 取消

2、右击->hosts->打开方式->选定记事本（或者你喜欢的编辑器）->在末尾处添加以下内容：

```shell
199.232.69.194 github.global.ssl.fastly.net
140.82.114.4 github.com
```

