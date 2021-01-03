MyISAM和InnoDB存储引擎使⽤的锁： 

MyISAM采⽤表级锁(table-level locking)。 

InnoDB⽀持⾏级锁(row-level locking)和表级锁,默认为⾏级锁 

表级锁和⾏级锁对⽐： 

表级锁： MySQL中锁定 粒度最⼤ 的⼀种锁，对当前操作的整张表加锁，实现简单，资源消耗也 ⽐᫾少，加锁快，不会出现死锁。其锁定粒度最⼤，触发锁冲突的概率最⾼，并发度最低， MyISAM和 InnoDB引擎都⽀持表级锁。 

⾏级锁： MySQL中锁定 粒度最⼩ 的⼀种锁，只针对当前操作的⾏进⾏加锁。 ⾏级锁能⼤⼤减 少数据库操作的冲突。其加锁粒度最⼩，并发度⾼，但加锁的开销也最⼤，加锁慢，会出现死 锁。 

InnoDB存储引擎的锁的算法有三种： 

Record lock：单个⾏记录上的锁 

Gap lock：间隙锁，锁定⼀个范围，不包括记录本身 

Next-key lock：record+gap 锁定⼀个范围，包含记录本身 

相关知识点： 

1. innodb对于⾏的查询使⽤next-key lock 
2. Next-locking keying为了解决Phantom Problem幻读问题 
3. 当查询的索引含有唯⼀属性时，将next-key lock降级为record key 
4. . Gap锁设计的⽬的是为了阻⽌多个事务将记录插⼊到同⼀范围内，⽽这会导致幻读问题的产⽣ 
5. 有两种⽅式显式关闭gap锁：（除了外键约束和唯⼀性检查外，其余情况仅使⽤record lock） A. 将事务隔离级别设置为RC B. 将参数innodb_locks_unsafe_for_binlog设置为1 