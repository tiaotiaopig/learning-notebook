# NoSQL

## nosql简介

非关系型数据库，**Not only SQL**

主要有四种存储结构：

+ 键值对存储（key-value）

  **redis ,tair , memcache**

+ 列存储

  **HBase**

  分布式文件系统

+ 文件存储

  **MongoDB**(Bson存储和传输，即二进制json)

  是一个基于分布式文件存储的数据库，C++实现，主要用于处理大量的文档

  是一个介于关系型和非关系型数据库的中间产品，是非关系型数据库中功能最丰富，最像关系型数据库的

  CouchDB

+ 图数据库（社交关系，广告推荐）

  Neo4j,infoGrid

![image-20210726095512695](https://i.loli.net/2021/07/26/PzAQcTj4oSdKagO.png)

## 基础入门

### 安装

redis是使用ANSI C语言编写，可以通过源码安装，通过gcc本地编译运行

编译成功后，相关命令都会放在`/usr/local/bin`中，编译直接执行`make`命令即可

1. 启动redis服务器

   `redis-server redis.conf(配置文件位置)`

2. 启动redis客户端

   `redis-cli -h localhost -p 6379`

   不指定主机和端口，就使用默认

3. 测试是否连接成功

   `ping`

4. 关闭redis

   在redis-cli中执行`shutdown`

### 基础命令

```bash
# 设置键值对
set key value
# 根据键获取值
get key
# key是否存在
exists key
# 获取所有键
keys *
# 切换数据库（默认有16个，在conf中配置）
select 3
# 数据库大小
dbsize
# 清空当前数据库
flushdb
# 清空所有数据库
flushall
# 数据在数据库中移动
move key db
# 删除数据
del key
# 设置过期时间
expire key second
# 查看过期时间
ttl key
# 查看数据类型
type 
```

### Redis 是单线程的

redis是基于内存操作的，CPU不是瓶颈，主要取决于机器的内存和网络带宽，使用单线程实现