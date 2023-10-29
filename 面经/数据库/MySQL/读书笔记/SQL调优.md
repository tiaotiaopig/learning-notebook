# SQL 调优思路

SQL调优这块呢，大厂面试必问的。最近金九银十嘛，所以整理了SQL的调优思路，并且附几个经典案例分析。

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpxtHVxOiagMUjb3RUGAYD1IzN7NEkyta7Oibny97QN3UKqPoAnrPrTklO1aHttzPO2TjFDFmYhvSpgA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

## 1.慢SQL优化思路。

1. 慢查询日志记录慢SQL
2. explain分析SQL的执行计划
3. profile 分析执行耗时
4. Optimizer Trace分析详情
5. 确定问题并采用相应的措施

### 1.1 慢查询日志记录慢SQL

如何定位慢SQL呢、我们可以通过**慢查询日志**来查看慢SQL。默认的情况下呢，MySQL数据库是不开启慢查询日志（`slow query log`）呢。所以我们需要手动把它打开。

查看下慢查询日志配置，我们可以使用`show variables like 'slow_query_log%'`命令，如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpxtHVxOiagMUjb3RUGAYD1IzbeibC0VsPaGW1uJhayibq7F90j6E5VBBWcJjU5QGLWGfGoLGdhd85ToA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- `slow query log`表示慢查询开启的状态
- `slow_query_log_file`表示慢查询日志存放的位置

我们还可以使用`show variables like 'long_query_time'`命令，查看超过多少时间，才记录到慢查询日志，如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpxtHVxOiagMUjb3RUGAYD1IzGj602Le7icibbXbObLJajjVT4vTU75Xs5EJjOvUfx2Sia9wXkQApykCEQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- `long_query_time`表示查询超过多少秒才记录到慢查询日志。

**我们可以通过慢查日志，定位那些执行效率较低的SQL语句，重点关注分析。**

### 1.2 explain查看分析SQL的执行计划

当定位出查询效率低的SQL后，可以使用`explain`查看`SQL`的执行计划。

当`explain`与`SQL`一起使用时，MySQL将显示来自优化器的有关语句执行计划的信息。即`MySQL`解释了它将如何处理该语句，包括有关如何连接表以及以何种顺序连接表等信息。

一条简单SQL，使用了`explain`的效果如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpxtHVxOiagMUjb3RUGAYD1IzXPXladfA4DnzaEHMtJZfpcDt1ibYkCj9B6eM3jiaQo8AA6ibKugI8w8mw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

一般来说，我们需要重点关注`type、rows、filtered、extra、key`。

#### 1.2.1 type

type表示**连接类型**，查看索引执行情况的一个重要指标。以下性能从好到坏依次：`system > const > eq_ref > ref > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL`

- system：这种类型要求数据库表中只有一条数据，是`const`类型的一个特例，一般情况下是不会出现的。
- const：通过一次索引就能找到数据，一般用于主键或唯一索引作为条件，这类扫描效率极高，，速度非常快。
- eq_ref：常用于主键或唯一索引扫描，一般指使用主键的关联查询
- ref : 常用于非主键和唯一索引扫描。
- ref_or_null：这种连接类型类似于`ref`，区别在于`MySQL`会额外搜索包含`NULL`值的行
- index_merge：使用了索引合并优化方法，查询使用了两个以上的索引。
- unique_subquery：类似于`eq_ref`，条件用了`in`子查询
- index_subquery：区别于`unique_subquery`，用于非唯一索引，可以返回重复值。
- range：常用于范围查询，比如：between ... and 或 In 等操作
- index：全索引扫描
- ALL：全表扫描

#### 1.2.2 rows

该列表示MySQL估算要找到我们所需的记录，需要读取的行数。对于InnoDB表，此数字是估计值，并非一定是个准确值。

#### 1.2.3 filtered

该列是一个百分比的值，表里符合条件的记录数的百分比。简单点说，这个字段表示存储引擎返回的数据在经过过滤后，剩下满足条件的记录数量的比例。

#### 1.2.4 extra

该字段包含有关MySQL如何解析查询的其他信息，它一般会出现这几个值：

- Using filesort：表示按文件排序，一般是在指定的排序和索引排序不一致的情况才会出现。一般见于order by语句
- Using index ：表示是否用了覆盖索引。
- Using temporary: 表示是否使用了临时表,性能特别差，需要重点优化。一般多见于group by语句，或者union语句。
- Using where : 表示使用了where条件过滤.
- Using index condition：MySQL5.6之后新增的索引下推。在存储引擎层进行数据过滤，而不是在服务层过滤，利用索引现有的数据减少回表的数据。

#### 1.2.5 key

该列表示实际用到的索引。一般配合`possible_keys`列一起看。

### 1.3 profile 分析执行耗时

`explain`只是看到`SQL`的预估执行计划，如果要了解`SQL`**真正的执行线程状态及消耗的时间**，需要使用`profiling`。开启`profiling`参数后，后续执行的`SQL`语句都会记录其资源开销，包括`IO，上下文切换，CPU，内存`等等，我们可以根据这些开销进一步分析当前慢SQL的瓶颈再进一步进行优化。

`profiling`默认是关闭，我们可以使用`show variables like '%profil%'`查看是否开启，如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpxtHVxOiagMUjb3RUGAYD1Iz2tcFhpAJmNRcWZ1WViavVZib1k0YRums6uibvibUcSNPHicibUiad5P0tvBQQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

可以使用`set profiling=ON`开启。开启后，可以运行几条SQL，然后使用`show profiles`查看一下。

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpxtHVxOiagMUjb3RUGAYD1IzS3KLpib5PjYVp8Z9CRYcEDVEdtVVfwKCV5Z1fuurC6sZpcXDt8X6KGQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

`show profiles`会显示最近发给服务器的多条语句，条数由变量`profiling_history_size`定义，默认是15。如果我们需要看单独某条SQL的分析，可以`show profile`查看最近一条SQL的分析，也可以使用`show profile for query id`（其中id就是show profiles中的QUERY_ID）查看具体一条的SQL语句分析。

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpxtHVxOiagMUjb3RUGAYD1IzNOEvds6F5Bp4DwGgYYeZiaUqeicmicfshDh8iaC8T9q0FgpP16ftbICu4Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

除了查看profile ，还可以查看cpu和io，如上图。

### 1.4 Optimizer Trace分析详情

profile只能查看到SQL的执行耗时，但是无法看到SQL真正执行的过程信息，即不知道MySQL优化器是如何选择执行计划。这时候，我们可以使用`Optimizer Trace`，它可以跟踪执行语句的解析优化执行的全过程。

我们可以使用`set optimizer_trace="enabled=on"`打开开关，接着执行要跟踪的SQL，最后执行`select * from information_schema.optimizer_trace`跟踪，如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpxtHVxOiagMUjb3RUGAYD1IzlkCjMoynGgc4BuPIPcTZWedLqfkRp5FEEbcYduCy6dwBPUUDweJreA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

大家可以查看分析其执行树，会包括三个阶段：

- join_preparation：准备阶段
- join_optimization：分析阶段
- join_execution：执行阶段

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpxtHVxOiagMUjb3RUGAYD1IzicK2JhjqkZAghEibiclu9a5aooh4qIZGphLquibt1bvheHoHuicfhicagYIQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 1.5 确定问题并采用相应的措施

最后确认问题，就采取对应的措施。

- 多数慢SQL都跟索引有关，比如不加索引，索引不生效、不合理等，这时候，我们可以**优化索引**。
- 我们还可以优化SQL语句，比如一些in元素过多问题（分批），深分页问题（基于上一次数据过滤等），进行时间分段查询
- SQl没办法很好优化，可以改用ES的方式，或者数仓。
- 如果单表数据量过大导致慢查询，则可以考虑分库分表
- 如果数据库在刷脏页导致慢查询，考虑是否可以优化一些参数，跟DBA讨论优化方案
- 如果存量数据量太大，考虑是否可以让部分数据归档

我之前写了一篇文章，有关于导致慢查询的12个原因，大家看一看一下哈:[盘点MySQL慢查询的12个原因](https://mp.weixin.qq.com/s?__biz=Mzg3NzU5NTIwNg==&mid=2247499624&idx=1&sn=561b9cb7fe831ca7cb2d9fd65691e85e&chksm=cf222041f855a957ac50c0a53baaec6d26be32427259b2974450620f33a8c834419fe535e83d&token=767319274&lang=zh_CN&scene=21#wechat_redirect)

## 2. 慢查询经典案例分析

### 2.1 案例1：隐式转换

我们创建一个用户user表

```
CREATE TABLE user (
  id int(11) NOT NULL AUTO_INCREMENT,
  userId varchar(32) NOT NULL,
  age  varchar(16) NOT NULL,
  name varchar(255) NOT NULL,
  PRIMARY KEY (id),
  KEY idx_userid (userId) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

`userId`字段为字串类型，是B+树的普通索引，如果查询条件传了一个数字过去，会导致索引失效。如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpxtHVxOiagMUjb3RUGAYD1Izia5JUxQBf0sEfUqAFByTTvcnSMgqcgadYxfblklAWhSvsP5fHrcvibGQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

如果给数字加上'',也就是说，传的是一个字符串呢，当然是走索引，如下图：

![图片](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1PmpxtHVxOiagMUjb3RUGAYD1IzqZeo6fSQyvwrHdMCzicyQucxZf3sH6fmEtEcwbIjbxZrsbXtJgWmheA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

> 为什么第一条语句未加单引号就不走索引了呢？这是因为不加单引号时，是字符串跟数字的比较，它们类型不匹配，MySQL会做隐式的类型转换，把它们转换为浮点数再做比较。隐式的类型转换，索引会失效。

### 2.2 案例2：最左匹配

MySQl建立联合索引时，会遵循最左前缀匹配的原则，即最左优先。如果你建立一个`（a,b,c）`的联合索引，相当于建立了`(a)、(a,b)、(a,b,c)`三个索引。

假设有以下表结构：

```
CREATE TABLE user (
  id int(11) NOT NULL AUTO_INCREMENT,
  user_id varchar(32) NOT NULL,
  age  varchar(16) NOT NULL,
  name varchar(255) NOT NULL,
  PRIMARY KEY (id),
  KEY idx_userid_name (user_id,name) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

假设有一个联合索引`idx_userid_name`，我们现在执行以下`SQL`，如果查询列是`name`，索引是无效的：

```
explain select * from user where name ='捡田螺的小男孩';
```



因为查询条件列`name`不是联合索引`idx_userid_name`中的第一个列，不满足最左匹配原则，所以索引不生效。在联合索引中，只有查询条件满足最左匹配原则时，索引才正常生效。如下，查询条件列是`user_id`



### 2.3 案例3：深分页问题

`limit`深分页问题，会导致慢查询，应该大家都司空见惯了吧。

**limit深分页为什么会变慢呢？** 假设有表结构如下：

```
CREATE TABLE account (
  id int(11) NOT NULL AUTO_INCREMENT COMMENT '主键Id',
  name varchar(255) DEFAULT NULL COMMENT '账户名',
  balance int(11) DEFAULT NULL COMMENT '余额',
  create_time datetime NOT NULL COMMENT '创建时间',
  update_time datetime NOT NULL ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (id),
  KEY idx_name (name),
  KEY idx_create_time (create_time) //索引
) ENGINE=InnoDB AUTO_INCREMENT=1570068 DEFAULT CHARSET=utf8 ROW_FORMAT=REDUNDANT COMMENT='账户表';
```

以下这个SQL，你知道执行过程是怎样的呢？

```
select id,name,balance from account where create_time> '2020-09-19' limit 100000,10;
```

这个SQL的执行流程酱紫：

1. 通过普通二级索引树`idx_create_time`，过滤`create_time`条件，找到满足条件的主键`id`。
2. 通过主键`id`，回到`id`主键索引树，找到满足记录的行，然后取出需要展示的列（回表过程）
3. 扫描满足条件的`100010`行，然后扔掉前`100000`行，返回。



因此，limit深分页，导致SQL变慢原因有两个：

- `limit`语句会先扫描`offset+n`行，然后再丢弃掉前`offset`行，返回后`n`行数据。也就是说`limit 100000,10`，就会扫描`100010`行，而`limit 0,10`，只扫描`10`行。
- `limit 100000,10` 扫描更多的行数，也意味着回表更多的次数。

**如何优化深分页问题?**

我们可以通过减少回表次数来优化。一般有**标签记录法和延迟关联法**。

**标签记录法**

> 就是标记一下上次查询到哪一条了，下次再来查的时候，从该条开始往下扫描。就好像看书一样，上次看到哪里了，你就折叠一下或者夹个书签，下次来看的时候，直接就翻到啦。

假设上一次记录到`100000`，则SQL可以修改为：

```
select  id,name,balance FROM account where id > 100000 limit 10;
```

这样的话，后面无论翻多少页，性能都会不错的，因为命中了id索引。但是这种方式有局限性：需要一种类似连续自增的字段。

**延迟关联法**

延迟关联法，就是把条件转移到**主键索引树**，然后减少回表。如下

```
select  acct1.id,acct1.name,acct1.balance FROM account acct1 INNER JOIN (SELECT a.id FROM account a WHERE a.create_time > '2020-09-19' limit 100000, 10) AS acct2 on acct1.id= acct2.id;
```

**优化思路**就是，先通过`idx_create_time`二级索引树查询到满足条件的`主键ID`，再与原表通过`主键ID`内连接，这样后面直接走了主键索引了，同时也减少了回表。

### 2.4  案例4：in元素过多

如果使用了`in`，即使后面的条件加了索引，还是要注意`in`后面的元素不要过多哈。`in`元素一般建议不要超过`200`个，如果超过了，建议分组，每次200一组进行哈。

**反例:**

```
select user_id,name from user where user_id in (1,2,3...1000000); 
```

如果我们对`in`的条件不做任何限制的话，该查询语句一次性可能会查询出非常多的数据，很容易导致接口超时。尤其有时候，我们是用的子查询，**in后面的子查询**，你都不知道数量有多少那种，更容易采坑.如下这种子查询：

```
select * from user where user_id in (select author_id from artilce where type = 1);
```

如果`type = 1`有1一千，甚至上万个呢？肯定是慢SQL。索引一般建议分批进行，一次200个，比如：

```
select user_id,name from user where user_id in (1,2,3...200);
```

in查询为什么慢呢？

> 这是因为`in`查询在MySQL底层是通过`n*m`的方式去搜索，类似`union`。
>
> in查询在进行cost代价计算时（代价 = 元组数 * IO平均值），是通过将in包含的数值，一条条去查询获取元组数的，因此这个计算过程会比较的慢，所以MySQL设置了个临界值(eq_range_index_dive_limit)，5.6之后超过这个临界值后该列的cost就不参与计算了。因此会导致执行计划选择不准确。默认是200，即in条件超过了200个数据，会导致in的代价计算存在问题，可能会导致Mysql选择的索引不准确。

### 2.5 order by 走文件排序导致的慢查询

如果order by 使用到文件排序，则会可能会产生慢查询。我们来看下下面这个SQL：

```
select name,age,city from staff where city = '深圳' order by age limit 10;
```

它表示的意思就是：查询前10个，来自深圳员工的姓名、年龄、城市，并且按照年龄小到大排序。



查看explain执行计划的时候，可以看到Extra这一列，有一个`Using filesort`，它表示用到文件排序。

**order by文件排序效率为什么较低**

大家可以看下这个下面这个图:



`order by`排序，分为`全字段排序`和`rowid排序`。它是拿`max_length_for_sort_data`和结果行数据长度对比，如果结果行数据长度超过`max_length_for_sort_data`这个值，就会走`rowid`排序，相反，则走全字段排序。

#### 2.5.1 rowid排序

rowid排序，一般需要回表去找满足条件的数据，所以效率会慢一点。以下这个SQL，使用rowid排序，执行过程是这样：

```
select name,age,city from staff where city = '深圳' order by age limit 10;
```

1. `MySQL`为对应的线程初始化`sort_buffer`，放入需要排序的`age`字段，以及`主键id`；
2. 从索引树`idx_city`， 找到第一个满足 `city='深圳’`条件的`主键id`,假设`id`为`X`；
3. 到主键`id索引树`拿到`id=X`的这一行数据， 取age和主键id的值，存到`sort_buffer`；
4. 从索引树`idx_city`拿到下一个记录的`主键id`，假设`id=Y`；
5. 重复步骤 3、4 直到`city`的值不等于深圳为止；
6. 前面5步已经查找到了所有`city`为深圳的数据，在`sort_buffer`中，将所有数据根据`age`进行排序；遍历排序结果，取前10行，并按照id的值回到原表中，取出`city、name 和 age`三个字段返回给客户端。



#### 2.5.2 全字段排序

同样的SQL，如果是走全字段排序是这样的：

```
select name,age,city from staff where city = '深圳' order by age limit 10;
```

1. MySQL 为对应的线程初始化`sort_buffer`，放入需要查询的`name、age、city`字段；
2. 从索引树`idx_city`， 找到第一个满足 `city='深圳’`条件的主键 id，假设找到`id=X`；
3. 到主键id索引树拿到`id=X`的这一行数据， 取`name、age、city`三个字段的值，存到`sort_buffer`；
4. 从索引树`idx_city` 拿到下一个记录的主键`id`，假设`id=Y`；
5. 重复步骤 3、4 直到`city`的值不等于深圳为止；
6. 前面5步已经查找到了所有`city`为深圳的数据，在`sort_buffer`中，将所有数据根据age进行排序；
7. 按照排序结果取前10行返回给客户端。



`sort_buffer`的大小是由一个参数控制的：`sort_buffer_size`。

- 如果要排序的数据小于`sort_buffer_size`，排序在`sort_buffer`内存中完成
- 如果要排序的数据大于`sort_buffer_size`，则借助磁盘文件来进行排序。

> 借助磁盘文件排序的话，效率就更慢一点。因为先把数据放入`sort_buffer`，当快要满时。会排一下序，然后把`sort_buffer`中的数据，放到临时磁盘文件，等到所有满足条件数据都查完排完，再用归并算法把磁盘的临时排好序的小文件，合并成一个有序的大文件。

#### 2.5.3  如何优化order by的文件排序

`order by`使用文件排序，效率会低一点。我们怎么优化呢？

- 因为数据是无序的，所以就需要排序。如果数据本身是有序的，那就不会再用到文件排序啦。而索引数据本身是有序的，我们通过建立索引来优化`order by`语句。
- 我们还可以通过调整`max_length_for_sort_data、sort_buffer_size`等参数优化；

### 2.6 索引字段上使用is null， is not null，索引可能失效

表结构:

```
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `card` varchar(255) DEFAULT NULL,
  `name` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `idx_name` (`name`) USING BTREE,
  KEY `idx_card` (`card`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
```

单个`name`字段加上索引，并查询`name`为非空的语句，其实会走索引的，如下:



单个`card`字段加上索引，并查询`name`为非空的语句，其实会走索引的，如下:



但是它两用or连接起来，索引就失效了，如下：



很多时候，也是因为数据量问题，导致了MySQL优化器放弃走索引。同时，平时我们用`explain`分析SQL的时候，如果`type=range`,要注意一下哈，因为这个可能因为数据量问题，导致索引无效。

### 2.7 索引字段上使用（！= 或者 < >），索引可能失效

假设有表结构：

```
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `userId` int(11) NOT NULL,
  `age` int(11) DEFAULT NULL,
  `name` varchar(255) NOT NULL,
  PRIMARY KEY (`id`),
  KEY `idx_age` (`age`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
```

虽然age加了索引，但是使用了！= 或者< >，not in这些时，索引如同虚设。如下：



其实这个也是跟mySQL优化器有关，如果优化器觉得即使走了索引，还是需要扫描很多很多行的哈，它觉得不划算，不如直接不走索引。平时我们用！= 或者< >，not in的时候，留点心眼哈。

### 2.8 左右连接，关联的字段编码格式不一样

新建两个表，一个`user`，一个`user_job`

```
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) CHARACTER SET utf8mb4 DEFAULT NULL,
  `age` int(11) NOT NULL,
  PRIMARY KEY (`id`),
  KEY `idx_name` (`name`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;

CREATE TABLE `user_job` (
  `id` int(11) NOT NULL,
  `userId` int(11) NOT NULL,
  `job` varchar(255) DEFAULT NULL,
  `name` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `idx_name` (`name`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

`user`表的`name`字段编码是`utf8mb4`，而`user_job`表的`name`字段编码为`utf8`。



执行左外连接查询,`user_job`表还是走全表扫描，如下：



如果把它们的name字段改为编码一致，相同的SQL，还是会走索引。



### 2.9 group by使用临时表

group by一般用于分组统计，它表达的逻辑就是根据一定的规则，进行分组。日常开发中，我们使用得比较频繁。如果不注意，很容易产生慢SQL。

#### 2.9.1 group by执行流程

假设有表结构：

```
CREATE TABLE `staff` (
  `id` bigint(11) NOT NULL AUTO_INCREMENT COMMENT '主键id',
  `id_card` varchar(20) NOT NULL COMMENT '身份证号码',
  `name` varchar(64) NOT NULL COMMENT '姓名',
  `age` int(4) NOT NULL COMMENT '年龄',
  `city` varchar(64) NOT NULL COMMENT '城市',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=15 DEFAULT CHARSET=utf8 COMMENT='员工表';
```

我们查看一下这个SQL的执行计划：

```
explain select city ,count(*) as num from staff group by city;
```



- Extra 这个字段的`Using temporary`表示在执行分组的时候使用了临时表
- Extra 这个字段的`Using filesort`表示使用了文件排序

group by是怎么使用到临时表和排序了呢？我们来看下这个SQL的执行流程

```
select city ,count(*) as num from staff group by city;
```

1. 创建内存临时表，表里有两个字段`city和num`；
2. 全表扫描staff的记录，依次取出city = 'X'的记录。

- 判断临时表中是否有为`city='X'`的行，没有就插入一个记录` (X,1)`;
- 如果临时表中有`city='X'`的行，就将X这一行的num值加 1；

1. 遍历完成后，再根据字段`city`做排序，得到结果集返回给客户端。这个流程的执行图如下：



**临时表的排序是怎样的呢？**

就是把需要排序的字段，放到sort buffer，排完就返回。在这里注意一点哈，排序分全字段排序和rowid排序

- 如果是全字段排序，需要查询返回的字段，都放入sort buffer，根据排序字段排完，直接返回
- 如果是rowid排序，只是需要排序的字段放入sort buffer，然后多一次回表操作，再返回。

#### 2.9.2 group by可能会慢在哪里？

`group by`使用不当，很容易就会产生慢`SQL`问题。因为它既用到临时表，又默认用到排序。有时候还可能用到磁盘临时表。

- 如果执行过程中，会发现内存临时表大小到达了上限（控制这个上限的参数就是`tmp_table_size`），会把内存临时表转成磁盘临时表。
- 如果数据量很大，很可能这个查询需要的磁盘临时表，就会占用大量的磁盘空间。

#### 2.9.3 如何优化group by呢

**从哪些方向去优化呢？**

- 方向1：既然它默认会排序，我们不给它排是不是就行啦。
- 方向2：既然临时表是影响group by性能的X因素，我们是不是可以不用临时表？

我们一起来想下，执行`group by`语句为什么需要临时表呢？`group by`的语义逻辑，就是统计不同的值出现的个数。如果这个这些值一开始就是有序的，我们是不是直接往下扫描统计就好了，就不用临时表来记录并统计结果啦?

可以有这些优化方案：

- group by 后面的字段加索引
- order by null 不用排序
- 尽量只使用内存临时表
- 使用SQL_BIG_RESULT

### 2.10  delete + in子查询不走索引！

之前见到过一个生产慢SQL问题，当delete遇到in子查询时，即使有索引，也是不走索引的。而对应的select + in子查询，却可以走索引。

MySQL版本是5.7，假设当前有两张表account和old_account,表结构如下：

```
CREATE TABLE `old_account` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键Id',
  `name` varchar(255) DEFAULT NULL COMMENT '账户名',
  `balance` int(11) DEFAULT NULL COMMENT '余额',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime NOT NULL ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  KEY `idx_name` (`name`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=1570068 DEFAULT CHARSET=utf8 ROW_FORMAT=REDUNDANT COMMENT='老的账户表';

CREATE TABLE `account` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键Id',
  `name` varchar(255) DEFAULT NULL COMMENT '账户名',
  `balance` int(11) DEFAULT NULL COMMENT '余额',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime NOT NULL ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  KEY `idx_name` (`name`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=1570068 DEFAULT CHARSET=utf8 ROW_FORMAT=REDUNDANT COMMENT='账户表';
```

执行的SQL如下：

```
delete from account where name in (select name from old_account);
```

查看执行计划，发现不走索引：

但是如果把delete换成select，就会走索引。如下：



为什么`select + in`子查询会走索引，`delete + in`子查询却不会走索引呢？

我们执行以下SQL看看：

```
explain select * from account where name in (select name from old_account);
show WARNINGS; //可以查看优化后,最终执行的sql
```

结果如下：

```
select `test2`.`account`.`id` AS `id`,`test2`.`account`.`name` AS `name`,`test2`.`account`.`balance` AS `balance`,`test2`.`account`.`create_time` AS `create_time`,`test2`.`account`.`update_time` AS `update_time` from `test2`.`account` 
semi join (`test2`.`old_account`)
where (`test2`.`account`.`name` = `test2`.`old_account`.`name`)
```

可以发现，实际执行的时候，MySQL对select in子查询做了优化，把子查询改成join的方式，所以可以走索引。但是很遗憾，对于`delete in`子查询，MySQL却没有对它做这个优化。

日常开发中，大家注意一下这个场景哈