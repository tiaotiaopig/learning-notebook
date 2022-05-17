# MySQLbinlog

## bin log(归档日志)

`redo log`它是物理日志，记录内容是“在某个数据页上做了什么修改”，属于`InnoDB`存储引擎。

而`binlog`是逻辑日志，记录内容是语句的原始逻辑，类似于“给ID=2这一行的c字段加1”，属于`MySQL Server`层。

不管用什么存储引擎，只要发生了表数据更新，都会产生`binlog`日志。

那`binlog`到底是用来干嘛的？

可以说`MySQL`数据库的**数据备份、主备、主主、主从**都离不开`binlog`，需要依靠`binlog`来同步数据，保证数据一致性。

`binlog`会记录所有涉及更新数据的逻辑操作，并且是顺序写.

## 记录格式

`binlog`日志有三种格式，可以通过`binlog_format`参数指定。

- **statement**
- **row**
- **mixed**

指定`statement`，记录的内容是`SQL`语句原文，比如执行一条`update T set update_time=now() where id=1`，记录的内容如下。

![图片](https://mmbiz.qpic.cn/mmbiz_png/23OQmC1ia8nxiaXqrSndLqJZv7ic9wSaRYtIct6NdhQicG44BZRlAicFZ60Kr5bmuvFWgN4fa3uicj5cYUNTejPiach9w/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

同步数据时，会执行记录的`SQL`语句，但是有个问题，`update_time=now()`这里会获取当前系统时间，直接执行会导致与原库的数据不一致。

为了解决这种问题，我们需要指定为`row`，记录的内容不再是简单的`SQL`语句了，还包含操作的具体数据，记录内容如下。

![图片](https://mmbiz.qpic.cn/mmbiz_png/23OQmC1ia8nxiaXqrSndLqJZv7ic9wSaRYt2RFw9v7vvUOPhMDhIR0yZa2QeD4PuWpKoUW2RV433icYuytoPqbIV9w/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

`row`格式记录的内容看不到详细信息，要通过`mysqlbinlog`工具解析出来。

`update_time=now()`变成了具体的时间`update_time=1627112756247`，条件后面的@1、@2、@3都是该行数据第1个~3个字段的原始值（**假设这张表只有3个字段**）。

这样就能保证同步数据的一致性，通常情况下都是指定为`row`，这样可以为数据库的恢复与同步带来更好的可靠性。

但是这种格式，需要更大的容量来记录，比较占用空间，恢复与同步时会更消耗`IO`资源，影响执行速度。

所以就有了一种折中的方案，指定为`mixed`，记录的内容是前两者的混合。

`MySQL`会判断这条`SQL`语句是否可能引起数据不一致，如果是，就用`row`格式，否则就用`statement`格式。

## 写入时机

`binlog`的写入时机也非常简单，事务执行过程中，先把日志写到`binlog cache`，事务提交的时候，再把`binlog cache`写到`binlog`文件中。

因为一个事务的`binlog`不能被拆开，无论这个事务多大，也要确保一次性写入，所以系统会给每个线程分配一个块内存作为`binlog cache`。

我们可以通过`binlog_cache_size`参数控制单个线程`binlog cache`大小，如果存储内容超过了这个参数，就要暂存到磁盘（`Swap`）。

`binlog`日志刷盘流程如下

![图片](https://mmbiz.qpic.cn/mmbiz_png/23OQmC1ia8nxiaXqrSndLqJZv7ic9wSaRYtrdpDibhNGocdGJebjZxtTl2JAUwN9DJu3W0gH5CuvY6Dcx5b8FGzXCA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- **上图的write，是指把日志写入到文件系统的page cache，并没有把数据持久化到磁盘，所以速度比较快**
- **上图的fsync，才是将数据持久化到磁盘的操作**

`write`和`fsync`的时机，可以由参数`sync_binlog`控制，默认是`0`。

为`0`的时候，表示每次提交事务都只`write`，由系统自行判断什么时候执行`fsync`。

虽然性能得到提升，但是机器宕机，`page cache`里面的`binglog`会丢失。

为了安全起见，可以设置为`1`，表示每次提交事务都会执行`fsync`，就如同**redolog日志刷盘流程**一样。

最后还有一种折中方式，可以设置为`N(N>1)`，表示每次提交事务都`write`，但累积`N`个事务后才`fsync`。

在出现`IO`瓶颈的场景里，将`sync_binlog`设置成一个比较大的值，可以提升性能。

同样的，如果机器宕机，会丢失最近`N`个事务的`binlog`日志。