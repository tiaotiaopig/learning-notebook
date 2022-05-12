# MySQL锁

## 前言

大家好，我是**捡田螺的小男孩**。本文将跟大家聊聊InnoDB的锁。本文比较长，包括一条SQL是如何加锁的，一些加锁规则、如何分析和解决死锁问题等内容，建议耐心读完，肯定对大家有帮助的。

1. 为什么需要加锁呢？
2. InnoDB的七种锁介绍
3. 一条SQL是如何加锁的
4. RR隔离级别下的加锁规则
5. 如何查看事务加锁情况
6. 死锁案例分析

## 1. 为什么需要加锁？

数据库为什么需要加锁呢？

> 在日常生活中，如果你心情不好。想要一个人静静，**不想被比别人打扰**，你就可以把自己关进房间里，并且**反锁**。

同理，对于MySQL数据库来说的话，一般的对象都是一个事务一个事务来说的。所以，如果一个事务内，正在写某个SQL，我们肯定**不想它被别的事务影响**到嘛？因此，数据库设计大叔，就给被操作的**SQL加上锁**。

> 专业一点的说法: 如果有多个并发请求存取数据，在数据就可能会产生多个事务同时操作同一行数据。如果并发操作不加控制，不加锁的话，就可能写入了不正确的数据，或者导致读取了不正确的数据，破坏了数据的一致性。因此需要考虑加锁。

### 1.1 事务并发存在的问题

- **脏读**:一个事务A读取到事务B未提交的数据，就是**脏读**。
- **不可重复读**：事务A被事务B干扰到了！在事务A范围内，两个相同的查询，读取同一条记录，却返回了不同的数据，这就是**不可重复读**。
- **幻读**：事务A查询一个范围的结果集，另一个并发事务B往这个范围中**插入/删除**了数据，并静悄悄地提交，然后事务A再次查询相同的范围，两次读取得到的结果集不一样了，这就是幻读。

### 1.2 一个加锁和不加锁对比的例子

我们知道MySQL数据库有四大隔离级别**读已提交（RC）、可重复读（RR）、串行化、读未提交**。如果是**读未提交隔离**级别，并发情况下，它是不加锁的，因此就会存在**脏读、不可重复读、幻读**的问题。

为了更通俗易懂一点，还是给大家举个例子吧，虽然东西挺简单的。假设现在有表结构和数据如下：

```
CREATE TABLE `account` (
  `id` int(11) NOT NULL,
  `name` varchar(255) DEFAULT NULL,
  `balance` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `un_name_idx` (`name`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
insert into account(id,name,balance)values (1,'Jay',100);
insert into account(id,name,balance)values (2,'Eason',100);
insert into account(id,name,balance)values (3,'Lin',100);
```

在**READ-UNCOMMITTED（读未提交）** 隔离级别下，假设现在有两个事务A、B：

- 假设现在Jay的余额是100，事务A正在准备查询Jay的余额
- 这时候，事务B先扣减Jay的余额，扣了10
- 最后A 读到的是扣减后的余额

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDrKnjjbia1kgE6Mbon6p6IMTugYjqG2OJypGddicCdf9pWBzyCpQbyntA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

手动验证了一把，流程如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDOhB8ib4bibpicD5l2icuV5bV7nPYmKibYAtnITxEEnPMKdUVMXANiatCzAdg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

由上图可以发现，事务A、B交替执行，事务A被事务B干扰到了，因为事务A读取到事务B未提交的数据,这就是脏读。为什么存在脏读问题呢？这是因为在**读未提交的隔离级别**下执行写操作，并**没有对SQL加锁**，因此产生了**脏读**这个问题。

我们再来看下，在**串行化隔离级别**下，同样的SQL执行流程，又是怎样的呢？

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDWcboaJTPKGHH67iaia7gWuBMDYf8kt410W7rNejVib2icB7gzwDuqr99cg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

为啥会阻塞等待超时呢？这是因为**串行化隔离级别**下，对写的SQL**加锁**啦。我们可以再看下加了什么锁，命令如下：

```
SET GLOBAL innodb_status_output=ON; -- 开启输出
SET GLOBAL innodb_status_output_locks=ON; -- 开启锁信息输出
SHOW ENGINE INNODB STATUS
```

锁相关的输出内容如下：![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuD7iapM0IDJvbib0By1UFoEy5nnwPVicxJCIR8hxHvPaQHFlqKpTdOVWDyA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

我们可以看到了这么一把锁：`lock_mode X locks rec but not gap`，它到底是一种什么锁呢？来来来，我们一起来学习下**InnoDB的七种锁**。

## 2. InnoDB的七种锁介绍

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuD67LR24YRYN6rg3QdvFPsC2VmIcjROcwhmFbHMOBSWfDH4UugZQpTfw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 2.1 共享/排他锁

InnoDB呢实现了两种标准的**行级锁**：共享锁（简称S锁）、排他锁（简称X锁）。

- 共享锁：简称为S锁，**在事务要读取一条记录时，需要先获取该记录的S锁。**
- 排他锁：简称X锁，**在事务需要改动一条记录时，需要先获取该记录的X锁。**

如果事务`T1`持有行R的`S`锁，那么另一个事务`T2`请求访问这条记录时，会做如下处理：

- T2 请求`S`锁立即被允许，结果` T1和T2`都持有R行的`S`锁
- T2 请求`X`锁不能被立即允许,此操作会阻塞

如果`T1`持有行R的`X`锁，那么`T2`请求R的`X、S`锁都不能被立即允许，`T2 `必须等待`T1`释放`X`锁才可以，因为`X`锁与任何的锁都不兼容。

`S锁和X锁`的兼容关系如下图表格：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDNG7fGyvOF2v36rlfj3jp6LlbxLFEEBhUab4P7JhHCib0QibJKcjMT01w/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

`X`锁和`S`锁是对于行记录来说的话，因此可以称它们为**行级锁或者行锁**。我们认为行锁的粒度就比较细，其实一个事务也可以在**表级别下加锁**，对应的，我们称之为**表锁**。给表加的锁，也是可以分为`X`锁和`S`锁的哈。

如果一个事务给表已经加了`S`锁，则：

- 别的事务可以继续获得该表的`S`锁，也可以获得该表中某些记录的`S`锁。
- 别的事务不可以继续获得该表的`X`锁，也不可以获得该表中某些记录的`X`锁。

如果一个事务给表加了`X`锁，那么

- 别的事务不可以获得该表的`S`锁，也不可以获得该表某些记录的`S`锁。
- 别的事务不可以获得该表的`X`锁，也不可以继续获得该表某些记录的`X`锁。

### 2.2 意向锁

什么是意向锁呢？意向锁是**一种不与行级锁冲突的表级锁**。未来的某个时刻，事务可能要加共享或者排它锁时，先提前声明一个意向。注意一下，意向锁，是一个**表级别的锁哈**。

**为什么需要意向锁呢？** 或者换个通俗的说法，为什么要加共享锁或排他锁时的时候，需要提前声明个意向锁呢呢？

> 因为InnoDB是支持表锁和行锁共存的，如果一个事务A获取到某一行的排他锁，并未提交，这时候事务B请求获取同一个表的表共享锁。因为**共享锁和排他锁是互斥的**，因此事务B想对这个表加共享锁时，需要保证没有其他事务持有这个表的表排他锁，同时还要保**证没有其他事务持有表中任意一行的排他锁**。
>
> 然后问题来了，你要保证没有其他事务持有表中任意一行的排他锁的话，去遍历每一行？这样显然是一个效率很差的做法。**为了解决这个问题，InnoDB的设计大叔提出了意向锁。**

**意向锁是如何解决这个问题的呢？** 我们来看下

意向锁分为两类：

- 意向共享锁：简称`IS`锁，当事务准备在某些记录上加S锁时，需要现在表级别加一个`IS`锁。
- 意向排他锁：简称`IX`锁，当事务准备在某条记录上加上X锁时，需要现在表级别加一个`IX`锁。

比如：

- `select ... lock in share mode`，要给表设置`IS`锁;
- `select ... for update`，要给表设置`IX`锁;

意向锁又是如何解决这个效率低的问题呢：

> 如果一个事务A获取到某一行的排他锁，并未提交,这时候表上就有`意向排他锁`和这一行的`排他锁`。这时候事务B想要获取这个表的共享锁，此时因为检测到事务A持有了表的`意向排他锁`，因此事务A必然持有某些行的排他锁，也就是说事务B对表的加锁请求需要阻塞等待，不再需要去检测表的每一行数据是否存在排他锁啦。这样效率就高很多啦。

意向锁仅仅表明意向的锁，意向锁之间**并不会互斥，是可以并行的**，整体兼容性如下图所示：

![图片](https://mmbiz.qpic.cn/mmbiz_png/sMmr4XOCBzGAfRLlzb3wnOaibJ5uRRiadqcfnQgZwNoqLEK36y8mYHF6FQzSyyPcUVjEjcpA4HJUQtfVicdedWgag/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 2.3 记录锁（Record Lock） 

记录锁是最简单的行锁，仅仅锁住一行。如：`SELECT c1 FROM t WHERE c1 = 10 FOR UPDATE`，如果c1字段是主键或者是唯一索引的话，这个SQL会加一个记录锁（Record Lock）

记录锁永远都是加在索引上的，即使一个表没有索引，InnoDB也会隐式的创建一个索引，并使用这个索引实施记录锁。它会阻塞其他事务对这行记录的插入、更新、删除。

一般我们看死锁日志时，都是找关键词，比如`lock_mode X locks rec but not gap`），就表示一个X型的记录锁。记录锁的关键词就是**rec but not gap**。以下就是一个记录锁的日志：

```
RECORD LOCKS space id 58 page no 3 n bits 72 index `PRIMARY` of table `test`.`t` 
trx id 10078 lock_mode X locks rec but not gap
Record lock, heap no 2 PHYSICAL RECORD: n_fields 3; compact format; info bits 0
 0: len 4; hex 8000000a; asc     ;;
 1: len 6; hex 00000000274f; asc     'O;;
 2: len 7; hex b60000019d0110; asc        ;;
```

### 2.4 间隙锁（Gap Lock）

为了解决幻读问题，InnoDB引入了间隙锁`(Gap Lock)`。间隙锁是一种加在两个索引之间的锁，或者加在第一个索引之前，或最后一个索引之后的间隙。它锁住的是**一个区间**，而不仅仅是这个区间中的每一条数据。

比如`lock_mode X locks gap before rec`表示X型gap锁。以下就是一个间隙锁的日志：

```
RECORD LOCKS space id 177 page no 4 n bits 80 index idx_name of table `test2`.`account` 
trx id 38049 lock_mode X locks gap before rec
Record lock, heap no 6 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 3; hex 576569; asc Wei;;
 1: len 4; hex 80000002; asc     ;;
```

### 2.5 临键锁(Next-Key Lock)

Next-key锁是**记录锁和间隙锁的组合**，它指的是加在某条记录以及这条记录前面间隙上的锁。说得更具体一点就是:临键锁会封锁索引记录本身，以及索引记录之前的区间，即它的锁区间是前开后闭，比如`(5,10]`。

如果一个会话占有了索引记录R的共享/排他锁，其他会话不能立刻在R之前的区间插入新的索引记录。官网是这么描述的：

> If one session has a shared or exclusive lock on record R in an index, another session cannot insert a new index record in the gap immediately before R in the index order.

### 2.6 插入意向锁

插入意向锁,是插入一行记录操作之前设置的**一种间隙锁。**这个锁释放了一种插入方式的信号。它解决的问题是：多个事务，在同一个索引，同一个范围区间插入记录时，如果插入的位置不冲突，就不会阻塞彼此。

假设有索引值4、7，几个不同的事务准备插入5、6，每个锁都在获得插入行的独占锁之前用插入意向锁各自锁住了4、7之间的间隙，但是不阻塞对方因为插入行不冲突。以下就是一个插入意向锁的日志：

```
RECORD LOCKS space id 31 page no 3 n bits 72 index `PRIMARY` of table `test`.`child`
trx id 8731 lock_mode X locks gap before rec insert intention waiting
Record lock, heap no 3 PHYSICAL RECORD: n_fields 3; compact format; info bits 0
 0: len 4; hex 80000066; asc    f;;
 1: len 6; hex 000000002215; asc     " ;;
 2: len 7; hex 9000000172011c; asc     r  ;;...
```

锁模式兼容矩阵（横向是已持有锁，纵向是正在请求的锁）如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/sMmr4XOCBzGAfRLlzb3wnOaibJ5uRRiadqJXn9OuWAEIRBl6A9Z6XCoy2JicmoPrF4n7E18JGgCcUWfv430g05QsQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 2.7 自增锁 

**自增锁是一种特殊的表级别锁**。它是专门针对`AUTO_INCREMENT`类型的列，对于这种列，如果表中新增数据时就会去持有自增锁。简言之，如果一个事务正在往表中插入记录，所有其他事务的插入必须等待，以便第一个事务插入的行，是连续的主键值。

官方文档是这么描述的：

> An AUTO-INC lock is a special table-level lock taken by transactions inserting into tables with AUTO_INCREMENT columns. In the simplest case, if one transaction is inserting values into the table, any other transactions must wait to do their own inserts into that table, so that rows inserted by the first transaction receive consecutive primary key values.

假设有表结构以及自增模式是1，如下：

```
mysql> create table t0 (id int NOT NULL AUTO_INCREMENT,name varchar(16),primary key ( id));

mysql> show variables like '%innodb_autoinc_lock_mode%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| innodb_autoinc_lock_mode | 1     |
+--------------------------+-------+
1 row in set, 1 warning (0.01 sec)
```

设置事务A和B交替执行流程如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuD6B9s799s8WKL5UPGjrccCShnN803CSbjYPxofAf948sicCwlBjOXnPw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

通过上图我们可以看到，当我们在事务A中进行自增列的插入操作时，另外会话事务B也进行插入操作，这种情况下会发生2个奇怪的现象：

- 事务A会话中的自增列好像直接增加了2个值。（如上图中步骤7、8）
- 事务B会话中的自增列直接从2开始增加的。（如上图步骤5、6）

自增锁是一个表级别锁，那为什么会话A事务还没结束，事务会话B可以执行插入成功呢？不是应该锁表嘛？

这是因为在参数`innodb_autoinc_lock_mode`上，这个参数设置为`1`的时候，相当于将这种`auto_inc lock`弱化为了一个更轻量级的互斥自增长机制去实现，官方称之为`mutex`。

`innodb_autoinc_lock_mode`还可以设置为0或者2，

- **0**：表示传统锁模式，使用`表级AUTO_INC`锁。一个事务的`INSERT-LIKE`语句在语句执行结束后释放AUTO_INC表级锁，而不是在事务结束后释放。
- **1**: 连续锁模式,连续锁模式对于`Simple inserts`不会使用表级锁，而是使用一个轻量级锁来生成自增值，因为InnoDB可以提前直到插入多少行数据。自增值生成阶段使用轻量级互斥锁来生成所有的值，而不是一直加锁直到插入完成。对于`bulk inserts`类语句使用AUTO_INC表级锁直到语句完成。
- **2**:交错锁模式,所有的`INSERT-LIKE`语句都不使用表级锁，而是使用轻量级互斥锁。

> - **INSERT-LIKE**:指所有的插入语句，包括：INSERT、REPLACE、INSERT…SELECT、REPLACE…SELECT,LOAD DATA等。
> - **Simple inserts**:指在插入前就能确定插入行数的语句，包括：INSERT、REPLACE，不包含INSERT…ON DUPLICATE KEY UPDATE这类语句。
> - **Bulk inserts**: 指在插入钱不能确定行数的语句，包括：INSERT … SELECT/REPLACE … SELECT/LOAD DATA。

## 3. 一条SQL是如何加锁的呢？

介绍完InnoDB的七种锁后，我们来看下一条SQL是如何加锁的哈，现在可以分9种情况进行：

- 组合一：查询条件是主键，RC隔离级别
- 组合二：查询条件是唯一索引，RC隔离级别
- 组合三：查询条件是普通索引，RC隔离级别
- 组合四：查询条件上没有索引，RC隔离级别
- 组合五：查询条件是主键，RR隔离级别
- 组合六：查询条件是唯一索引，RR隔离级别
- 组合七：查询条件是普通索引，RR隔离级别
- 组合八：查询条件上没有索引，RR隔离级别
- 组合九：Serializable隔离级别

### 3.1  查询条件是主键 + RC隔离级别

在**RC（读已提交）** 的隔离级别下，对查询条件列是主键id的话，会加什么锁呢？

我们搞个简单的表，初始化几条数据：

```
create table t1 (id int,name varchar(16),primary key ( id));
insert into t1 values(1,'a'),(3,'c'),(6,'b'),(9,'a'),(10,'d');
```

假设给定SQL：`delete from t1 where id = 6;`，id是主键。在RC隔离级别下，只需要将主键上`id = 6`的记录，加上`X锁`即可。

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDicb2uTGN41WshLy8bibBNkgn6woiclHrtqtdJib9GtQ2nlhAPeHHicwWwSA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

我们来验证一下吧，先开启事务会话A，先执行以下操作：

```
begin;
//删除id=6的这条记录
delete from t1 where id = 6;
```

接着开启事务会话B

```
begin;
update t1 set name='b1' where id =6;
//发现这个阻塞等待，最后超时释放锁了
```

验证流程图如下：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

事务会话B对`id=6`的记录执行更新时，发现阻塞了，打开看下加了什么锁。发现是因为`id=6`这一行加了一个X型的记录锁

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

如果我们事务B不是对`id=6`执行更新，而是其他记录的话，是可以顺利执行的，如下：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

**结论就是**，在**RC（读已提交）** 的隔离级别下，对查询条件是主键id的场景，会加一个排他锁（X锁），或者说加一个X型的记录锁。

### 3.2 查询条件是唯一索引+RC隔离级别

如果查询条件id，只是一个唯一索引呢？那在RC（读提交隔离级别下），又加了什么锁呢？我们搞个新的表，初始化几条数据：

```
create table t2 (name varchar(16),id int,primary key (name),unique key(id));
insert into t2 values('a',1),('c',3),('b',6),('d',9);
```

id是唯一索引，name是主键的场景下，我们给定SQL：`delete from t2 where id = 6;`。

在RC隔离级别下，该SQL需要加两个`X`锁，一个对应于id 唯一索引上的`id = 6`的记录，另一把锁对应于聚簇索引上的`[name=’b’,id=6]`的记录。

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDEF0e6Yiby2Lxu2uM4yBYbSTNlGX0KVnxk5n5VFVtok6ACgWCtRM4SLw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**为什么主键索引上的记录也要加锁呢？**

> 如果并发的一个SQL，是通过主键索引来更新：`update t2 set id = 666 where name = 'b';`此时，如果delete语句没有将主键索引上的记录加锁，那么并发的update就会感知不到delete语句的存在，违背了同一记录上的更新/删除需要串行执行的约束。

### 3.3  查询条件是普通索引 + RC隔离级别

如果查询条件是普通的二级索引，在RC（读提交隔离级别下），又加了什么锁呢？

> 若id列是普通索引，那么对应的所有满足SQL查询条件的记录，都会加上锁。同时，这些记录对应主键索引，也会上锁。

我们初始化下表结构和数据

```
create table t3 (name varchar(16),id int,primary key (name),key(id));
insert into t3 values('a',1),('c',3),('b',6),('e',6),('d',9);
```

加锁示意图如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDsjhOnuLNctOdE7c4nyeZEz6svs9R3w5RMRcl1xG0ibJA9awnV3T2icQQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

我们来验证一下，先开启事务会话A，先执行以下操作：

```
begin;
//删除id=6的这条记录
delete from t3 where id = 6;
```

接着开启事务会话B

```
begin;
update t3 set id=7 where name ='e';
//发现这个阻塞等待，最后超时释放锁了
```

实践流程如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDUH4zZOxHy6xg8TuWeCs7aeTRICt8M7kvg5IHky7y61WEldbibtLohRA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

事务会话B为什么会阻塞等待超时，是因为事务会话A的`delete语句`确实有加**主键索引的X锁**

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDYpXfnnqGPvw4lQfVV5ZL82Ck0w2ajZj6rNrgbia98EcAT26mwDhZAuw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 3.4 查询条件列无索引+RC隔离级别

如果id没有加索引，只是一个常规的列，在RC（读提交隔离级别下），又加了什么锁呢？

> 若id列上没有索引，MySQL会走聚簇索引进行全表扫描过滤。每条记录都会加上X锁。但是，**为了效率考虑，MySQL在这方面进行了改进**，在扫描过程中，**若记录不满足过滤条件，会进行解锁操作**。同时优化违背了2PL原则。

初始化下表结构和数据

```
create table t4 (name varchar(16),id int,primary key (name));
insert into t4 values('a',1),('c',3),('b',6),('e',6),('d',9);
```

加锁示意图图下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDGWOfO4icF4VWcGgAu1BPE3PFojxiaVszdKfQXB7olS87gLt5PicOpsLfg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

验证流程如下，先开启事务会话A，先执行以下操作：

```
begin;
//删除id=6的这条记录
delete from t4 where id = 6;
```

接着开启事务会话B

```
begin;
//可以执行，MySQL因为效率问题，解锁了
update t4 set name='f' where id=3;
//阻塞等待
update t4 set name='f' where id=6;
```

验证结果如下：![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDhWNYMpxtQqfnbqZJx78UMgVcc0vX54NjLCtdZwsG8UPj3vU4hrhDxQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 3.5 查询条件是主键+RR隔离级别

给定SQL：`delete from t1 where id = 6;`，如果id是**主键**的话，在RR隔离级别下，跟RC隔离级别，加锁是一样的，也都是在`id = 6`这条记录上加上`X`锁。大家感兴趣可以照着3.1小节例子，自己验证一下哈。

### 3.6 查询条件是唯一索引+RR隔离级别

给定SQL：`delete from t1 where id = 6;`，如果id是**唯一索引**的话，在RR隔离级别下，跟RC隔离级别，加锁也是一样的哈，加了两个`X`锁，id唯一索引满足条件的记录上一个，对应的主键索引上的记录一个。

### 3.7 查询条件是普通索引+RR隔离级别

如果查询条件是普通的二级索引，在RR（可重复读的隔离级别下），除了会加`X`锁，**还会加`间隙Gap`锁**。Gap锁的提出，是为了解决幻读问题引入的，它是一种加在两个索引之间的锁。

假设有表结构和初始化数据如下：

```
CREATE TABLE t5 ( id int(11) NOT NULL, c int(11) DEFAULT NULL, d int(11) DEFAULT NULL, PRIMARY KEY (id), KEY c (c)) ENGINE=InnoDB;
insert into t5 values(5,5,5),(10,10,10),(15,15,15),(20,20,20),(25,25,25);
```

如果一条更新语句`update t5 set d=d+1 where c = 10`，加锁示意图如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDNJGMRBPl2yvtB0maHW33Q0Z2kGdIIOsCRNqwRE13Hib0d9R4ANH53qg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

我们来验证一下吧，先开启事务会话A，先执行以下操作：

```
begin;
update t5 set d=d+1 where c = 10;
```

接着开启事务会话B

```
begin;
insert into t5 values(12,12,12);
//阻塞等待，最后超时释放锁了
```

验证流程图如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDPWAKTxxFVfTbePZialsqxRseH1hZLLAjn68IPHwPwibewH1CN7AKxrqA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

为什么会阻塞呢？因此`c=10`这个记录更新时，不仅会有两把`X`锁，还会把区间`（10,15）`加间隙锁，因此要插入`（12,12,12）`记录时，会阻塞。

### 3.8 查询条件列无索引+RR隔离级别

如果查询条件列没有索引呢？又是如何加的锁呢？

假设有表结构和数据如下：

```
CREATE TABLE t5 ( id int(11) NOT NULL, c int(11) DEFAULT NULL, d int(11) DEFAULT NULL, PRIMARY KEY (id)) ENGINE=InnoDB;
insert into t5 values(5,5,5),(10,10,10),(15,15,15),(20,20,20),(25,25,25);
```

给定一条更新语句`update t5 set d=d+1 where c = 10`，因为`c`列没有索引，加锁示意图如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDshYcAWbV1tWfuHKqyztSYmpSy1F0X9ZMPibdibykeJagjeJVUboEZoNw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

如果查询条件列没有索引，主键索引的所有记录，都将加上`X锁`，每条记录间也都加上`间隙Gap锁`。大家可以想象一下，任何加锁并发的SQL，都是不能执行的，全表都是锁死的状态。如果表的数据量大，那效率就更低。

> 在这种情况下，MySQL做了一些优化，即`semi-consistent read`，对于不满足条件的记录，MySQL提前释放锁，同时Gap锁也会释放。而`semi-consistent read`是如何触发的呢：要么在`Read Committed`隔离级别下；要么在`Repeatable Read`隔离级别下，设置了`innodb_locks_unsafe_for_binlog`参数。但是`semi-consistent read`本身也会带来其他的问题，不建议使用。

我们来验证一下哈，先开启事务会话A，先执行以下操作：

```
begin;
update t5 set d=d+1 where c = 20;
```

接着开启事务会话B

```
begin;
insert into t5 values(16,16,16);
//插入阻塞等待
update t5 set d=d+1 where c = 16;
//更新阻塞等待
```

我们去更新一条不存在的`c=16`的记录，也会被X锁阻塞的。验证如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDaDcu2LWfLDJoa2N7vSnLt6ic5eN2X9y7b0VRXefOtansphyLXR5ia8dw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 3.9 Serializable串行化

在**Serializable串行化的隔离级别**下，对于写的语句，比如`update account set balance= balance-10 where name=‘Jay’;`，跟RC和RR隔离级别是一样的。不一样的地方是，在查询语句，如`select balance from account where name = ‘Jay’;`，在**RC和RR**是不会加锁的，但是在Serializable串行化的隔离级别，即会加锁。

如文章开始第一小节的那个例子，就是类似的：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuD6ibGG5rJs9nlfjO1INOzNqz5B9gQCtxNBiaKZXqOZvW24XK2bw1RxGyw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

## 4. RR隔离级别下，加锁规则到底是怎样的呢？

对于RC隔离级别，加的排他锁（X锁），是比较好理解的，哪里更新就锁哪里嘛。但是**RR隔离级别**，间隙锁是怎么加的呢？我们一起来学习一下。

对**InnoDb**的锁来说，面试的时候问的比较多，就是`Record lock、Gap lock、Next-key lock`。接下来我们来学习，RR隔离级别，到底一个锁是怎么加上去的。**丁奇的MySQL45讲有讲到，RR隔离级别，是如何加锁的。大家有兴趣可以去订购看下哈，非常不错的课程。**

首先MySQL的版本，是`5.x 系列 <=5.7.24，8.0 系列 <=8.0.13`。加锁规则一共包括：两个`原则`、`两个优化`和一个`bug`。

- **原则1**：加锁的基本单位都是`next-key lock`。`next-key lock（临键锁）`是前开后闭区间。
- **原则2**：查找过程中访问到的对象才会加锁。
- **优化1**：索引上的等值查询，给唯一索引加锁的时候，`next-key lock`退化为行锁`（Record lock）`。
- **优化 2**：索引上的等值查询，向右遍历时且最后一个值不满足等值条件的时候，`next-key lock`退化为间隙锁（Gap lock）。
- **一个 bug**：唯一索引上的范围查询会访问到不满足条件的第一个值为止。

假设有表结构和数据如下：

```
CREATE TABLE t5 ( id int(11) NOT NULL, c int(11) DEFAULT NULL, d int(11) DEFAULT NULL, PRIMARY KEY (id), KEY c (c)) ENGINE=InnoDB;
insert into t5 values(0,0,0),(5,5,5),(10,10,10),(15,15,15),(20,20,20),(25,25,25);
```

分7个案例去分析哈：

1. 等值查询间隙锁
2. 非唯一索引等值锁
3. 主键索引范围锁
4. 非唯一索引范围锁
5. 唯一索引范围锁 bug
6. 普通索引上存在"等值"的例子
7. limit 语句减少加锁范围

### 4.1 案例一：等值查询间隙锁

我们同时开启A、B、C三个会话事务，如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDU9ehRiaDBYNA7zxicwzDg2ClN7McnGMGJf1B2xBknTvfxs7Jk4XqEZXQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

发现事务B会阻塞等待，而C可以执行成功。如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDIx8kIjg7FiattD38LOh0Nuj3ERj6Q96w1Psjsmk47TMhWZfmmw63HrQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**为什么事务B会阻塞呢？**

- 这是因为根据**加锁原则1**：加锁基本单位是`next-key lock`，因此事务会话 A的加锁范围是（5,10]，这里为什么是区间（5,10]，这是因为更新的记录，所在的表已有数据的区间就是5-10哈，又因为`next-key lock`是左开右闭的，所以加锁范围是`（5,10]`。
- 同时**根据优化 2**，这是一个等值查询 (id=6)，而id=10不满足查询条件。所以`next-key lock`退化成间隙`Gap锁`，因此最终加锁的范围是`(5,10)`。
- 然后`事务Session B`中，你要插入的是9,9在区间(5,10)内，而区间(5,10)都被锁了。因此事务B会阻塞等到。

**为什么事务C可以正常执行呢？**

这是因为锁住的区间是`(5,10)`，没有包括10，**所以事务C可以正常执行**。

### 4.2 案例二：非唯一索引等值锁

按顺序执行事务会话A、B、C，如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDhwpIaU7NpSLUvAH25feUUzWr4394LywicoD62SCkNfbQf4aaRWzPnhg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

发现事务B可以执行成功，而C阻塞等待。如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDOibmLibPUC3IntBLBzNsiaq4G8U5adsK00LTCMYEsAPSW8pVmqXOIRRJQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**为什么事务会话B没有阻塞，事务会话C却阻塞了？**

事务会话A执行时，会给索引树`c=5`的这一行加上读`共享`锁。

1. 根据**加锁原则1**，加锁单位是`next-key lock`，因此会加上`next-key lock(0,5]`。
2. 因为c 只是普通索引，所以仅访问`c=5`这一条记录时不会马上停下来，需要继续向右遍历，查到`c=10`才结束。根据**加锁原则2**，访问到的都要加锁，因此要给`(5,10]`加`next-key lock`。
3. 由**加锁优化2**：等值判断，向右遍历，最后一个值10`不满足c=5` 这个等值条件，因此退化成间隙锁 `(5,10)`。
4. 根据**加锁原则 2** ：**只有访问到的对象才会加锁**，事务A的这个查询使用了**覆盖索引**，没有回表，并不需要访问主键索引，因此主键索引上没有加任何锁，事务会话B是对主键id的更新，因此事务会话B的`update`语句不会阻塞。
5. 但是事务会话C，要插入一个（6,6,6) 的记录时，会被事务会话A的`间隙锁(5,10)`锁住，因此事务会话C阻塞了。

### 4.3 案例三：主键索引范围锁

主键范围查询又是怎么加锁的呢？比如给定SQL:

```
select * from t5 where id>=10 and id<11 for update;
```

按顺序执行事务会话A、B、C，如下：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

执行结果如下：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

发现事务会话B中，插入12，即`insert into t5 values(12,12,12);`时，阻塞了，而插入6，`insert into t5 values(6,6,6);`却可以顺利执行。同时事务C中，`Update t5 set d=d+1 where id =15;`也会阻塞，为什么呢？

事务会话A执行时，要找到第一个`id=10`的行：

- 根据**加锁原则1**：加锁单位是`next-key lock`，因此会加上`next-key lock(5,10]`。
- 又因为id是主键，也就是唯一值，因此根据**优化1**：索引上的等值查询，给唯一索引加锁时，`next-key lock`退化为`行锁（Record lock）`。所以只加了`id=10`这个行锁。
- 范围查找就往后继续找，找到`id=15`这一行停下来，因此还需要加`next-key lock(10,15]`。

事务会话A执行完后，加的锁是`id=10`这个行锁，以及临键锁`next-key lock(10,15]`。这就是为什么事务B插入6那个记录可以顺利执行，插入12就不行啦。同理，事务C那个更新id=15的记录，也是会被阻塞的。

### 4.4 案例四：非唯一索引范围锁

如果是普通索引，范围查询又加什么锁呢？按顺序执行事务会话A、B、C，如下：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

执行结果如下：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

发现事务会话B和事务会话C的执行SQL都被阻塞了。

这是因为，事务会话A执行时，要找到第一个`c=10`的行：

1. 根据加锁原则1：加锁单位是next-key lock，因此会加上`next-key lock(5,10]`。又因为c不是唯一索引，所以它不会退化为行锁。因此加的锁还是`next-key lock(5,10]`。
2. 范围查找就往后继续找，找到`id=15`这一行停下来，因此还需要加`next-key lock(10,15]`。

因此事务B和事务C插入的`insert into t5 values(6,6,6);`和`Update t5 set d=d+1 where c =15;` 都会阻塞。

### 4.5 案例五：唯一索引范围锁 bug

前面四种方案中，加锁的两个原则和两个优化都已经用上啦，那个唯一索引范围bug是如何触发的呢？

按顺序执行事务会话A、B、C，如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDBIYBgVfrhNu7eBlIHhmncYia6sA4syfBJTubRWN6htqjBfJfmrAiberw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

执行结果如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDdwUG2qYC2nzs4pH43dAsGKAxiaNDT5xS5vibZyH7XBnPSw9P6PQufD0Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

发现事务B的更新语句`Update t5 set d=d+1 where id =20; `和事务C`insert into t5 values(18,18,18);`的插入语句均已阻塞了。

这是因为，事务会话A执行时，要找到第一个`id=15`的行，根据加锁原则1：加锁单位是`next-key lock`，因此会加上`next-key lock(10,15]`。因为id是主键，即唯一的，因此循环判断到 id=15 这一行就应该停止了。但是实现上，InnoDB 会往前扫描到第一个不满足条件的行为止，直到扫描到`id=20`。而且由于这是个范围扫描，因此索引id上的`(15,20]`这个 next-key lock 也会被锁上。

所以，事务B要更新 id=20 这一行时，会阻塞锁住。同样地事务会话C要插入`id=16`的一行，也会被锁住。

### 4.6  案例六：普通索引上存在"等值"的例子

如果查询条件列是普通索引，且存在相等的值，加锁又是怎样的呢？

在原来t5表的数据基础上，插入：

```
insert into t5 values(28,10,66);
```

则`c索引`树如下：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

c索引值有相等的，但是它们对应的主键是有间隙的。比如`（c=10，id=10）和（c=10，id=28）`之间。

我们来看个例子，按顺序执行事务会话A、B、C，如下：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

执行结果如下：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

为什么事务B插入语句会阻塞，事务C的更新语句不会呢？

- 这是因为事务会话A在遍历的时候，先访问第一个`c=10`的记录。它根据**原则 1**，加一个(c=5,id=5) 到 (c=10,id=10)的next-key lock。
- 然后，事务会话A向右查找，直到碰到 (c=15,id=15) 这一行，循环才结束。根据优化 2，这是一个等值查询，向右查找到了不满足条件的行，所以会退化成`(c=10,id=10) 到 (c=15,id=15)`的间隙Gap锁。即事务会话A这个`select...for update`语句在索引 c 上的加锁范围，就是下图灰色阴影部分的：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

因为c=13是这个区间内的，所以事务B插入`insert into t5 values(13,13,13);`会阻塞。因为根据优化2，已经退化成 (c=10,id=10) 到 (c=15,id=15) 的间隙Gap锁,即不包括c=15，所以事务C，`Update t5 set d=d+1 where c=15`不会阻塞

### 4.7 案例七：limit 语句减少加锁范围

如果一个SQL有limit，会不会对加锁有什么影响呢？我们用4.6的例子，然后给查询语句加个limit：

```
Select * from t5 where c=10 limit 2 for update;
```

事务A、B执行如下：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

发现事务B并没有阻塞，而是可以顺利执行![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

这是为什么呢？跟上个例子，怎么事务会话B的SQL却不会阻塞了，事务会话A的`select`只是加多了一个`limit 2`。

这是因为明确加了`limit 2`的限制后，因此在遍历到 (c=10, id=30) 这一行之后，满足条件的语句已经有两条，循环就结束了。因此，索引 c上的加锁范围就变成了从（c=5,id=5) 到（c=10,id=30) 这个前开后闭区间，如下图所示：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

索引平时我们写SQL的时候，比如`查询select或者delete语句`时，尽量加一下`limit`哈，你看着这个例子不就减少了锁范围了嘛，哈哈。

## 5. 如何查看事务加锁情况

我门怎么查看执行中的SQL加了什么锁呢？或者换个说法，如何查看事务的加锁情况呢？有这两种方法：

- 使用`infomation_schema`数据库中的表获取锁信息
- 使用show engine innodb status 命令

### 5.1 使用infomation_schema数据库中的表获取锁信息

`infomation_schema`数据库中，有几个表跟锁紧密关联的。

- **INNODB_TRX**：该表存储了InnoDB当前正在执行的事务信息，包括事务id、事务状态（比如事务是在运行还是在等待获取某个所）等。
- **INNODB_LOCKS**：该表记录了一些锁信息，包括两个方面：1.如果一个事务想要获取某个锁，但未获取到，则记录该锁信息。2. 如果一个事务获取到了某个锁，但是这个锁阻塞了别的事务，则记录该锁信息。
- **INNODB_LOCK_WAITS**:表明每个阻塞的事务是因为获取不到哪个事务持有的锁而阻塞。

#### 5.1.1 INNODB_TRX

我们在一个会话中执行加锁的语句，在另外一个会话窗口，即可查看`INNODB_TRX`的信息啦，如下：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

表中可以看到一个事务id为`1644837`正在运行汇中，它的隔离级别为`REPEATABLE READ`。我们一般关注这几个参数：

- trx_tables_locked：该事务当前加了多少个表级锁。
- trx_rows_locked：表示当前加了多少个行级锁。
- trx_lock_structs：表示该事务生成了多少个内存中的锁结构。

#### 5.1.2 INNODB_LOCKS

一般系统中，发生某个事务**因为获取不到锁而被阻塞时**，该表才会有记录。

事务A、B执行如下：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

使用`select * from information_schema.INNODB_LOCKS;`查看

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

可以看到两个事务Id `1644842`和`1644843`都持有什么锁，就是看那个`lock_mode和lock_type`哈。但是并看不出是哪个锁在等待那个锁导致的阻塞，这时候就可以看`INNODB_LOCK_WAITS`表啦。

#### 5.1.3 INNODB_LOCK_WAITS

INNODB_LOCK_WAITS 表明每个事务是因为获取不到哪个事务持有的锁而阻塞。

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

- requesting_trx_id：表示因为获取不到锁而被阻塞的事务的事务id
- blocking_trx_id：表示因为获取到别的事务需要的锁而导致其被阻塞的事务的事务Id。

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

即`requesting_trx_id`表示事务B的事务Id，`blocking_trx_id`表示事务A的事务Id。

### 5.2 show engine innodb status

**INNODB_LOCKS** 和 **INNODB_LOCK_WAITS** 在MySQL 8.0已被移除，其实就是不鼓励我们用这两个表来获取表信息。而我们还可以用`show engine innodb status`获取当前系统各个事务的加锁信息。

在看死锁日志的时候，我们一般先把这个变量`innodb_status_output_locks`打开哈，它是MySQL 5.6.16 引入的

```
set global  innodb_status_output_locks =on;
```

在RR隔离级别下，我们交替执行事务A和B：

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

show engine innodb status查看日志，如下：

```
TRANSACTIONS
------------
Trx id counter 1644854
Purge done for trx's n:o < 1644847 undo n:o < 0 state: running but idle
History list length 32
LIST OF TRANSACTIONS FOR EACH SESSION:
---TRANSACTION 283263895935640, not started
0 lock struct(s), heap size 1136, 0 row lock(s)
---TRANSACTION 1644853, ACTIVE 7 sec inserting
mysql tables in use 1, locked 1
LOCK WAIT 2 lock struct(s), heap size 1136, 1 row lock(s), undo log entries 1
MySQL thread id 7, OS thread handle 11956, query id 563 localhost ::1 root update
insert into t5 values(6,6,6)
------- TRX HAS BEEN WAITING 7 SEC FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 267 page no 4 n bits 80 index c of table `test2`.`t5` trx id 1644853 lock_mode X locks gap before rec insert intention waiting
Record lock, heap no 3 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 4; hex 8000000a; asc     ;;
 1: len 4; hex 8000000a; asc     ;;

------------------
TABLE LOCK table `test2`.`t5` trx id 1644853 lock mode IX
RECORD LOCKS space id 267 page no 4 n bits 80 index c of table `test2`.`t5` trx id 1644853 lock_mode X locks gap before rec insert intention waiting
Record lock, heap no 3 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 4; hex 8000000a; asc     ;;
 1: len 4; hex 8000000a; asc     ;;
```

这结构锁的关键词需要记住一下哈：

- `lock_mode X locks gap before rec`表示X型的gap锁
- `lock_mode X locks rec but not gap`表示 X型的记录锁（Record Lock）
- `lock mode X` 一般表示 X型临键锁（next-key 锁）

以上的锁日志，我们一般关注点，是一下这几个地方：

- `TRX HAS BEEN WAITING 7 SEC FOR THIS LOCK TO BE GRANTED`表示它在等这个锁
- `RECORD LOCKS space id 267 page no 4 n bits 80 index c of table `test2`.`t5` trx id 1644853 lock_mode X locks gap before rec insert intention waiting`表示一个锁结构，这个锁结构的Space ID是267，page number是4，n_bits属性为80，对应的索引是`c`，这个锁结构中存放的锁类型是X型的插入意向Gap锁。
- `0: len 4; hex 8000000a; asc ;;`对应加锁记录的详细信息，8000000a代表的值就是10，a的16进制是10。
- `TABLE LOCK table `test2`.`t5` trx id 1644853 lock mode IX `表示一个插入意向表锁

这个日志例子，其实理解起来，就是事务A持有了索引c的间隙锁`（~，10）`，而事务B想获得这个gap锁，而获取不到，就一直在等待这个插入意向锁。

## 6. 手把手死锁案例分析

如果发生死锁了，我们应该如何分析呢？一般分为四个步骤：

1. `show engine innodb status`，查看最近一次死锁日志。
2. 分析死锁日志，找到关键词`TRANSACTION`
3. 分析死锁日志，查看正在执行的SQL
4. 看SQL持有什么锁，又在等待什么锁。

### 6.1 一个死锁的简单例子

表结构和数据如下：

```
CREATE TABLE t6 ( id int(11) NOT NULL, c int(11) DEFAULT NULL, d int(11) DEFAULT NULL, PRIMARY KEY (id), KEY c (c)) ENGINE=InnoDB;
insert into t6 values(5,5,5),(10,10,10);
```

我们开启A、B事务，执行流程如下：![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

### 6.2 分析死锁日志

1. `show engine innodb status`，查看最近一次死锁日志。如下：

```
------------------------
LATEST DETECTED DEADLOCK
------------------------
2022-05-03 22:53:22 0x2eb4
*** (1) TRANSACTION:
TRANSACTION 1644867, ACTIVE 31 sec starting index read
mysql tables in use 1, locked 1
LOCK WAIT 3 lock struct(s), heap size 1136, 2 row lock(s)
MySQL thread id 5, OS thread handle 7068, query id 607 localhost ::1 root statistics
Select * from t6 where id=10 for update
*** (1) WAITING FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 268 page no 3 n bits 72 index PRIMARY of table `test2`.`t6` trx id 1644867 lock_mode X locks rec but not gap waiting
Record lock, heap no 3 PHYSICAL RECORD: n_fields 5; compact format; info bits 0
 0: len 4; hex 8000000a; asc     ;;
 1: len 6; hex 00000019193c; asc      <;;
 2: len 7; hex dd00000191011d; asc        ;;
 3: len 4; hex 8000000a; asc     ;;
 4: len 4; hex 8000000a; asc     ;;

*** (2) TRANSACTION:
TRANSACTION 1644868, ACTIVE 17 sec starting index read, thread declared inside InnoDB 5000
mysql tables in use 1, locked 1
3 lock struct(s), heap size 1136, 2 row lock(s)
MySQL thread id 7, OS thread handle 11956, query id 608 localhost ::1 root statistics
Select * from t6 where id=5 for update
*** (2) HOLDS THE LOCK(S):
RECORD LOCKS space id 268 page no 3 n bits 72 index PRIMARY of table `test2`.`t6` trx id 1644868 lock_mode X locks rec but not gap
Record lock, heap no 3 PHYSICAL RECORD: n_fields 5; compact format; info bits 0
 0: len 4; hex 8000000a; asc     ;;
 1: len 6; hex 00000019193c; asc      <;;
 2: len 7; hex dd00000191011d; asc        ;;
 3: len 4; hex 8000000a; asc     ;;
 4: len 4; hex 8000000a; asc     ;;

*** (2) WAITING FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 268 page no 3 n bits 72 index PRIMARY of table `test2`.`t6` trx id 1644868 lock_mode X locks rec but not gap waiting
Record lock, heap no 2 PHYSICAL RECORD: n_fields 5; compact format; info bits 0
 0: len 4; hex 80000005; asc     ;;
 1: len 6; hex 00000019193c; asc      <;;
 2: len 7; hex dd000001910110; asc        ;;
 3: len 4; hex 80000005; asc     ;;
 4: len 4; hex 80000005; asc     ;;
```

1. 先找到关键词`TRANSACTION`，可以发现两部分的事务日志，如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDqESpKkm1PEwAzRTEJf6beSdicBHKs3hTDVfWyicVqxSu4gOxsnN9V54A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

1. 查看正在执行，产生死锁的对应的SQL，如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDjNW5qaFE8eibCCd7AgJbRlp2eWiawNZffJxEP94wcAHrkk8aa8iajkPhg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

1. 查看分开两部分的TRANSACTION，分别持有什么锁，和等待什么锁。

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpyZvxqe41tKhryLqKtd5LuDKG0zc3suQlvQibxaqJvpYCcEuVIoXX5ozRY5bbBKP7qNibuIsX9ibpTmg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

所谓的死锁，其实就是，我持有你的需要的锁，你持有我需要的锁，形成相互等待的闭环。所以排查死锁问题时，照着这个思维去思考就好啦。