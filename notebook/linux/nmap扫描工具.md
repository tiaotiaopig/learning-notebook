# namp 扫描工具

## 快速上手

 可以扫描域名,IP地址,网段
  Ex: scanme.nmap.org, microsoft.com/24, 192.168.0.1; 10.0.0-255.1-254

主要功能有:主机发现,端口扫描,服务/版本探测,操作系统探测,防火墙/IDS逃避和欺骗,结果输出

```bash
# 扫描主机,CIDR地址
# 将会扫描192.168.50.0 - 192.168.50.255 共255个ip地址
nmap 192.168.50.0/24
# 指定具体范围
nmap 192.168.50.14-25
# 使用文件指定主机名
# 用-iL 把文件名作为选项传给Nmap。列表中的项可以是Nmap在 命令行上接受的任何格式(IP地址，主机名，CIDR，IPv6，或者八位字节范围)。 每一项必须以一个或多个空格，制表符或换行符分开。
nmap -iL <inputfilename>
```

## 常用命令

### 主机发现

+ `-sP ping`,查看哪些ip有主机在运行

> nmap -sP 192.168.50.1-20

+ `-PS [portlist]` (TCP SYN Ping) 该选项发送一个设置了SYN标志位的空TCP报文
+ `-PA [portlist]` (TCP ACK Ping) 设置TCP的ACK标志位而不是SYN标志位

提供SYN和ACK两种ping探测的原因是使通过防火墙的机会尽可能大。 许多管理员会配置他们的路由器或者其它简单的防火墙来封锁SYN报文.

由于没头没脑的ACK报文 通常会被识别成伪造的而丢弃。解决这个两难的方法是通过即指定 `-PS`又指定`-PA`来即发送SYN又发送ACK。

+ `-PU [portlist]` (UDP Ping) 该扫描类型的主要优势是它可以穿越只过滤TCP的防火墙和过滤器。
+ `-sS` (TCP SYN扫描)
+ `-sT` (TCP connect()扫描)
+ `-sU` (UDP扫描)
+ `-sA` (TCP ACK扫描)
+ `-sW` (TCP窗口扫描)

> nmap -sV -n 192.168.50.17 -oX nmap.xml