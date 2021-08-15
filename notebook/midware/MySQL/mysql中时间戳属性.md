# MySQL 时间戳属性设置

> timestamp有两个属性，分别是**CURRENT_TIMESTAMP** 和**ON UPDATE CURRENT_TIMESTAMP**两种，使用情况分别如下：
>
> 1. CURRENT_TIMESTAMP 
>
> 当要向数据库执行insert操作时，如果有个timestamp字段属性设为 
>
> CURRENT_TIMESTAMP，则无论这个字段有木有set值都插入当前系统时间 
>
> 2. ON UPDATE CURRENT_TIMESTAMP
>
> 当执行update操作是，并且字段有ON UPDATE CURRENT_TIMESTAMP属性。则字段无论值有没有变化，他的值也会跟着更新为当前UPDATE操作时的时间。
>
>  
>
> TIMESTAMP的变体
>
> 1. TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP  
>
> 在创建新记录和修改现有记录的时候都对这个数据列刷新
>
> 2. TIMESTAMP DEFAULT CURRENT_TIMESTAMP 
>
> 在创建新记录的时候把这个字段设置为当前时间，但以后修改时，不再刷新它
>
> 3. TIMESTAMP ON UPDATE CURRENT_TIMESTAMP 
>
> 在创建新记录的时候把这个字段设置为0，以后修改时刷新它 
>
> 4. TIMESTAMP DEFAULT ‘yyyy-mm-dd hh:mm:ss’ ON UPDATE CURRENT_TIMESTAMP  
>
> 在创建新记录的时候把这个字段设置为给定值，以后修改时刷新它

我的理解:

1. CURRENT_TIMESTAMP一般在**创建时间**设置,它是**插入**时刷新
2. ON UPDATE CURRENT_TIMESTAMP 一般在**修改时间**设置,它是**修改**时刷新
3. 两个设置也可以同时使用,用作修改时间

