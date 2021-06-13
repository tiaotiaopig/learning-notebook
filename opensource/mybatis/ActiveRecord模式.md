# ActiveRecord模式

## ActiveRecord

ActiveRecord 也属于 ORM 层，由 Rails 最早提出，遵循标准的 ORM 模型：表映射到记录，记录映射到对象，字段映射到对象属性。配合遵循的命名和配置惯例，能够很大程度的快速实现模型的操作，而且简洁易懂。

## 主要思想

1. 每一个数据库表对应创建一个类，类的每一个对象实例对应于数据库中表的一行记录；通常表的每个字段在类中都有相应的 Field ；
2. ActiveRecord 同时负责把自己持久化，在 ActiveRecord 中封装了对数据库的访问，即 CURD; ；
3. ActiveRecord 是一种领域模型 (Domain Model) ，封装了部分业务逻辑；

## 使用范围

1. 业务逻辑比较简单，当你的类基本上和数据库中的表一一对应时 , ActiveRecord 是非常方便的，即你的业务逻辑大多数是对单表操作；
2.  当发生跨表的操作时 , 往往会配合使用事务脚本 (Transaction Script) ，把跨表事务提升到事务脚本中；
3. ActiveRecord 最大优点是简单 , 直观。 一个类就包括了数据访问和业务逻辑 . 如果配合代码生成器使用就更方便了；
4. 这些优点使 ActiveRecord 特别适合 WEB 快速开发。