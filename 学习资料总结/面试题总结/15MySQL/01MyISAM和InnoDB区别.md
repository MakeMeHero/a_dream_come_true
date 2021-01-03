MyISAM是MySQL的默认数据库引擎（5.5版之前）。虽然性能极佳，⽽且提供了⼤量的特性，包括全⽂索 引、压缩、空间函数等，但MyISAM不⽀持事务和⾏级锁，⽽且最⼤的缺陷就是崩溃后⽆法安全恢复。不 过，5.5版本之后，MySQL引⼊了InnoDB（事务性数据库引擎），MySQL 5.5版本后默认的存储引擎为 InnoDB。 

⼤多数时候我们使⽤的都是 InnoDB 存储引擎，但是在某些情况下使⽤ MyISAM 也是合适的⽐如读密集 的情况下。（如果你不介意 MyISAM 崩溃恢复问题的话）。 

两者的对⽐ 

1. 是否⽀持⾏级锁 : MyISAM 只有表级锁(table-level locking)，⽽InnoDB ⽀持⾏级锁(rowlevel locking)和表级锁,默认为⾏级锁。 
2. 是否⽀持事务和崩溃后的安全恢复： MyISAM 强调的是性能，每次查询具有原⼦性,其执⾏速度 ⽐InnoDB类型更快，但是不提供事务⽀持。但是InnoDB 提供事务⽀持事务，外部键等⾼级数据 库功能。 具有事务(commit)、回滚(rollback)和崩溃修复能⼒(crash recovery capabilities) 的事务安全(transaction-safe (ACID compliant))型表。 
3. 是否⽀持外键： MyISAM不⽀持，⽽InnoDB⽀持。 
4. 是否⽀持MVCC ：仅 InnoDB ⽀持。应对⾼并发事务, MVCC⽐单纯的加锁更⾼效;MVCC只在 READ COMMITTED 和 REPEATABLE READ 两个隔离级别下⼯作;MVCC可以使⽤ 乐观 (optimistic)锁 和 悲观(pessimistic)锁来实现;各数据库中MVCC实现并不统⼀。 