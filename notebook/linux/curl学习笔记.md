# curl学习笔记

## curl简介

    curl 是常用的命令行工具，用来请求 Web 服务器。它的名字就是客户端（client）的 URL 工具的意思。

    它的功能非常强大，命令行参数多达几十种。如果熟练的话，完全可以取代 Postman 这一类的图形界面工具。

## curl命令行参数
---

* 不带有任何参数时，curl 就是发出 GET 请求。下面命令向www.example.com发出 GET 请求，服务器返回的内容会在命令行输出

    > `curl https://www.example.com`

* **-A** 参数指定客户端的用户代理标头，即User-Agent。curl的默认用户代理字符串是curl/[version]

    * 下面命令将User-Agent改成 Chrome 浏览器
        > `curl -A 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36' https://google.com`

    * 下面命令会移除User-Agent标头
        > `curl -A '' https://google.com`

    * 也可以通过-H参数直接指定标头，更改User-Agent
        > `curl -H 'User-Agent: php/1.0' https://google.com`

---
* **-b** 参数用来向服务器发送 Cookie

    * 下面命令会生成一个标头Cookie: foo=bar，向服务器发送一个名为foo、值为bar的 Cookie
        > `curl -b 'foo=bar' https://google.com`

    * 下面命令发送两个 Cookie
        > `curl -b 'foo1=bar;foo2=bar2' https://google.com`

    * 下面命令读取本地文件cookies.txt，里面是服务器设置的 Cookie（参见-c参数），将其发送到服务器
        > `curl -b cookies.txt https://www.google.com`


