---
date created: 2022-07-27 11:21
---

Tag: #MySQL存储引擎
Link:

- [MySQL存储引擎InnoDB与Myisam的六大区别 | 菜鸟教程](https://www.runoob.com/w3cnote/mysql-different-nnodb-myisam.html)

> 摘要: MySQL有多种存储引擎，每种存储引擎有各自的优缺点，可以择优选择使用：MyISAM、InnoDB、MERGE、MEMORY(HEAP)、BDB(BerkeleyDB)、EXAMPLE、FEDERATED、ARCHIVE、CSV、BLACKHOLE。

|          | MyISAM | InnoDB |
| -------- | ------ | ------ |
| 构成上的区别  |        |        |
| 事务处理上方面 |        |        |
|  SELECT UPDATE,INSERT，Delete操作 |        |        |
|  对AUTO_INCREMENT的操作 |     如果执行大量的`SELECT`，`MyISAM`是更好的选择    |     1.如果你的数据执行大量的`INSERT`或`UPDATE`，出于性能方面的考虑，应该使用`InnoDB`表 2.`DELETE FROM table`时，`InnoDB`不会重新建立表，而是一行一行的删除。3.`LOAD TABLE FROM MASTER`操作对`InnoDB`是不起作用的，解决方法是首先把`InnoDB`表改成`MyISAM`表，导入数据后再改成`InnoDB`表，但是对于使用的额外的`InnoDB`特性（例如外键）的表不适用    |
|  表的具体行数  |        |        |
|   锁  |    表锁     |     提供行锁(locking on row level)，提供与 Oracle 类型一致的不加锁读取(non-locking read in SELECTs)，另外，InnoDB表的行锁也不是绝对的，如果在执行一个SQL语句时MySQL不能确定要扫描的范围，InnoDB表同样会锁全表， 例如update table set num=1 where name like "%aaa%"    |

