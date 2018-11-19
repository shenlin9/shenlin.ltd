---
title: MySQL - Manual - SQL Syntax - 13.1.34 - TRUNCATE TABLE 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - TRUNCATE TABLE
---

MySQL `TRUNCATE TABLE` 语法

<!--more-->

```
TRUNCATE [TABLE] tbl_name
```

`TRUNCATE TABLE` 会彻底清空一个表，需要 `DROP` 特权。逻辑上，`TRUNCATE TABLE` 类
似于一条删除所有行的 `DELETE` 语句，或者是一个由 `DROP TABLE` 和 `CREATE TABLE`
组成的语句序列。

为了获得高性能，`TRUNCATE TABLE` 绕过了删除数据的 DML 方法。因此，它不会触发 `ON
DELETE` 触发器，也不能用于具有父-子外键关系的 InnoDB 表，也不能像 DML 操作那样回
滚。但是，对于使用支持原子 DDL 存储引擎的表，如果服务器在 `TRUNCATE TABLE` 操作
期间停止，则 `TRUNCATE TABLE` 操作要么完全提交，要么回滚。有关更多信息，请参阅
Section 13.1.1, “Atomic Data Definition Statement Support”。

尽管 `TRUNCATE TABLE` 类似于 `DELETE`，但它被分类为 DDL 语句而不是 DML 语句。它
与 `DELETE` 的不同之处在于:
* 截断操作直接删除表并重建表，这比逐行删除要快得多，特别是对于大型表。
* 截断操作导致隐式提交，因此不能回滚。请参阅 Section 13.3.3,“Statements That
  Cause an Implicit Commit”
* 如果会话持有活动的表锁，则无法执行截断操作。
* 对 InnoDB 表或 NDB 表执行 `TRUNCATE TABLE` 时，如果存在引用该表的其他表的
  `FOREIGN KEY` 约束，则将失败。但同一个表不同列之间的外键约束则允许。
* 截断操作不会为删除的行数返回有意义的值。通常的结果是 `0 rows affected`，应该解
  释为 `no information`。
* 只要表定义有效，即使数据或索引文件已损坏，也可以使用 `TRUNCATE TABLE` 将表重新
  创建为空表。
* 任何 `AUTO_INCREMENT` 值将重置为其初始值。甚至对于 MyISAM 和 InnoDB 也是如此，
  它们通常不会重用序列值。
* 与分区表一起使用时，`TRUNCATE TABLE` 保留了分区;也就是说，数据和索引文件被删除
  和重新创建，而分区定义不受影响。
* `TRUNCATE TABLE` 语句不调用 `ON DELETE` 触发器。
* 支持截断损坏的 InnoDB 表。

表的 `TRUNCATE TABLE` 关闭了用 `HANDLER OPEN` 打开表的所有处理程序。

`TRUNCATE TABLE` is treated for purposes of binary logging and replication as `DROP TABLE` followed by `CREATE TABLE`—that is, as DDL rather than DML. This is due to the fact that, when using InnoDB and other transactional storage engines where the transaction isolation level does not permit statement-based logging (`READ COMMITTED` or `READ UNCOMMITTED`), the statement was not logged and replicated when using `STATEMENT` or `MIXED` logging mode. (Bug #36763) However, it is still applied on replication slaves using InnoDB in the manner described previously.
`TRUNCATE TABLE` 作为二进制日志记录和复制的目的被处理为 `DROP TABLE` 后跟着 `CREATE TABLE`，即 DDL 而不是 DML。这是因为，当使用 InnoDB 和其他事务存储引擎时，事务隔离级别不允许基于语句的日志记录(`READ COMMITTED` 或 `READ UNCOMMITTED`)，在使用 `STATEMENT` 或 `MIXED` 日志记录模式时，不会记录和复制语句。(Bug #36763) 然而，它仍然被应用于使用 InnoDB 的复制从服务器上，就像前面描述的那样。

在 MySQL 5.7 和更早的版本中，在一个具有大缓冲池和启用了
`innodb_adaptive_hash_index` 的系统上，由于删除表的自适应哈希索引项(Bug #68184)
时发生了 LRU 扫描，一个 `TRUNCATE TABLE` 操作可能会导致系统性能暂时下降。

在 MySQL 8.0 中将 `TRUNCATE TABLE` 重新映射为 `DROP TABLE` 和 `CREATE TABLE` 可
以避免 LRU 扫描问题。

`TRUNCATE TABLE` 可以与性能模式摘要表(Performance Schema summary tables)一起使用
，但其效果是将摘要列重置为 0 或 NULL，而不是删除行。参见 Section 25.11.15, “
Performance Schema Summary Tables”。


