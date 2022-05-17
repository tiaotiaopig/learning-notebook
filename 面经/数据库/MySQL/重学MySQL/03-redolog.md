# MySQL redolog日志

MySQL的三个日志:**binlog**,**redolog**,**undolog**

一个是`InnoDB`存储引擎的`redo log`（重做日志）`undo log`(回滚日志)，另一个是`MySQL Servce`层的 `binlog`（归档日志）。

其中,`undo log`在MVCC中有用到,还可以保证事务的原子性.

## redo log

`redo log`（重做日志）是`InnoDB`存储引擎独有的，它让`MySQL`拥有了崩溃恢复能力。

比如`MySQL`实例挂了或宕机了，重启时，`InnoDB`存储引擎会使用`redo log`恢复数据，保证数据的持久性与完整性。

`MySQL`中数据是以页为单位，你查询一条记录，会从硬盘把一页的数据加载出来，加载出来的数据叫数据页，会放入到`Buffer Pool`中。

后续的查询都是先从`Buffer Pool`中找，没有命中再去硬盘加载，减少硬盘`IO`开销，提升性能。

更新表数据的时候，也是如此，发现`Buffer Pool`里存在要更新的数据，就直接在`Buffer Pool`里更新。

然后会把“在某个数据页上做了什么修改”记录到重做日志缓存（`redo log buffer`）里，接着刷盘到`redo log`文件里。

![图片](https://mmbiz.qpic.cn/mmbiz_png/23OQmC1ia8nzoia78ia1wnynufibsPx05L54zUDHSoo2miaeyicIo2SGBY0FicnkbWeicrTlQH0LenmpScjibL35u61KVoQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

理想情况，事务一提交就会进行刷盘操作，但实际上，刷盘的时机是根据策略来进行的。

> 每条redo记录由“表空间号+数据页号+偏移量+修改数据长度+具体修改的数据”组成

## 刷盘时机

`InnoDB`存储引擎为`redo log`的刷盘策略提供了`innodb_flush_log_at_trx_commit`参数，它支持三种策略

- **设置为0的时候，表示每次事务提交时不进行刷盘操作**
- **设置为1的时候，表示每次事务提交时都将进行刷盘操作（默认值）**
- **设置为2的时候，表示每次事务提交时都只把redo log buffer内容写入page cache**

另外`InnoDB`存储引擎有一个后台线程，每隔`1`秒，就会把`redo log buffer`中的内容写到文件系统缓存（`page cache`），然后调用`fsync`刷盘。

![图片](https://mmbiz.qpic.cn/mmbiz_png/23OQmC1ia8nzoia78ia1wnynufibsPx05L54Ad70tZojSrwI8YOGP7ibboticxTic0pmOk6FClqx08AA75BictzAdJDD7g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

也就是说，一个没有提交事务的`redo log`记录，也可能会刷盘。

为什么呢？

因为在事务执行过程`redo log`记录是会写入`redo log buffer`中，这些`redo log`记录会被后台线程刷盘。

![图片](https://mmbiz.qpic.cn/mmbiz_png/23OQmC1ia8nzoia78ia1wnynufibsPx05L54v2N1so73Jm9TKRrmQCyA3dxNmMgwJhCiaNYrKyXBxv5ydMQm9GRIhUg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

除了后台线程每秒`1`次的轮询操作，还有一种情况，当`redo log buffer`占用的空间即将达到`innodb_log_buffer_size`一半的时候，后台线程会主动刷盘。

> 出现异常(MySQL实例(进程)挂了或者机器宕机),三种策略的后果:
>
> 0. 事务提交不刷盘,依靠后台线程每秒刷盘,可能会丢失1秒内的数据
> 1. 事务提交时刷盘,则不会丢失数据,因为挂掉导致事务失败,数据本就不应该保存
> 2. 事务提交只写入缓存,实例挂掉,后台线程还可以将缓存刷盘,机器宕机则缓存丢失

## 日志文件组

硬盘上存储的`redo log`日志文件不只一个，而是以一个**日志文件组**的形式出现的，每个的`redo`日志文件大小都是一样的。

比如可以配置为一组`4`个文件，每个文件的大小是`1GB`，整个`redo log`日志文件组可以记录`4G`的内容。

它采用的是环形数组形式，从头开始写，写到末尾又回到头循环写，如下图所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/23OQmC1ia8nzoia78ia1wnynufibsPx05L54XsdKnbUPKlA4OUSQY709Mhr5G55YB6TiadxyrtVQXUzCJ0HbRiaKw5zg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

在个**日志文件组**中还有两个重要的属性，分别是`write pos、checkpoint`

- **write pos是当前记录的位置，一边写一边后移**
- **checkpoint是当前要擦除的位置，也是往后推移**

每次刷盘`redo log`记录到**日志文件组**中，`write pos`位置就会后移更新。

每次`MySQL`加载**日志文件组**恢复数据时，会清空加载过的`redo log`记录，并把`checkpoint`后移更新。

`write pos`和`checkpoint`之间的还空着的部分可以用来写入新的`redo log`记录。

![图片](https://mmbiz.qpic.cn/mmbiz_png/23OQmC1ia8nzoia78ia1wnynufibsPx05L54ep2l4ibKADGr0jytCgLvDW1G8mibfoyn0GNtFXQzSnuu9lhIAL4sGw4Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

如果`write pos`追上`checkpoint`，表示**日志文件组**满了，这时候不能再写入新的`redo log`记录，`MySQL`得停下来，清空一些记录，把`checkpoint`推进一下。

## 为什么使用 redo log

实际上，数据页大小是`16KB`，刷盘比较耗时，可能就修改了数据页里的几`Byte`数据，有必要把完整的数据页刷盘吗？

而且数据页刷盘是随机写，因为一个数据页对应的位置可能在硬盘文件的随机位置，所以性能是很差。

如果是写`redo log`，一行记录可能就占几十`Byte`，只包含表空间号、数据页号、磁盘文件偏移 量、更新值，再加上是顺序写，所以刷盘速度很快。

所以用`redo log`形式记录修改内容，性能会远远超过刷数据页的方式，这也让数据库的并发能力更强。

> 其实内存的数据页在一定时机也会刷盘，我们把这称为页合并，讲`Buffer Pool`的时候会对这块细说

总结:redo log 只记录修改信息,不会保存整条记录,再加之是顺序写,所以效率很高.