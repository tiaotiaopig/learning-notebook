# InnoDB记录存储结构

> 1. InnoDB是将表中**数据存储到磁盘**的存储引擎,**数据处理在内存**中,需要将磁盘中的数据加载到内存.
> 2. 以页作为磁盘和内存交互的基本单位,默认大小16KB,一次内存和磁盘交互最少一页的数据.
> 3. 以记录为单位