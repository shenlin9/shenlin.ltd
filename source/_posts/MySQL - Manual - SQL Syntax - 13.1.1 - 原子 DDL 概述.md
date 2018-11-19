---
title: MySQL - Manual - SQL Syntax - 13.1.1 - 原子 DDL 概述
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
---

MySQL 原子数据定义语句

<!--more-->

MySQL 8.0 支持**原子数据定义语言语句**，这个特性被称为原子 DDL(Data Definition
Language, 数据定义语言)。一个原子 DDL 语句把和 DDL 操作关联的数据字典更新、存储
引擎操作和二进制日志写入合并到单个原子事务中。事务要么被提交，对数据字典、存储引
擎和二进制日志的更改持久化适用，要么被回滚，即使服务器在操作期间暂停。

通过在 MySQL 8.0 中引入 MySQL 数据字典，使原子 DDL 成为可能。在早期的 MySQL 版本
中，元数据存储在元数据文件、非事务性表和特定存储引擎的字典中，这需要中间提交。
MySQL 数据字典提供的集中式、事务性元数据存储消除了这一障碍，使得将 DDL 语句操作
重构为原子事务成为可能。

原子 DDL 特性在本节的以下主题进行了描述:
* Supported DDL Statements
* Atomic DDL Characteristics
* Changes in DDL Statement Behavior
* Storage Engine Support
* Viewing DDL Logs

## Supported DDL Statements

原子 DDL 特性支持表 DDL 语句和非表 DDL 语句。表相关的 DDL 操作需要存储引擎支持，
而非表 DDL 操作不需要。目前，只有 InnoDB 存储引擎支持原子 DDL 。

* 受支持的表 DDL 语句包括：数据库、表空间、表和索引的 `CREATE`，`ALTER`，`DROP`
  语句，以及 `TRUNCATE TABLE` 语句。

* 受支持的非表 DDL 语句包括：

  * `CREATE` 和 `DROP` 语句，如果适用，还包括用于存储程序、触发器、视图和用户定
    义函数(UDFs)的 `ALTER` 语句。

  * 账户管理语句：`CREATE`，`ALTER`，`DROP`，如果适用，还包括用于用户和角色的
    `RENAME`，`GRANT`，`REVOKE` 语句。

原子 DDL 特性不支持以下语句：
* 涉及到除 InnoDB 之外的存储引擎的与表相关的 DDL 语句。
* `INSTALL PLUGIN` 和 `UNINSTALL PLUGIN` 语句
* `INSTALL COMPONENT` 和 `UNINSTALL COMPONENT` 语句
* `CREATE SERVER`, `ALTER SERVER`, `DROP SERVER` 语句

## Atomic DDL Characteristics

原子 DDL 语句的特征包括:

* 元数据更新、二进制日志写入和存储引擎操作(如果适用)合并到了单个事务中。

* 在 DDL 操作期间，SQL 层没有中间提交。

* 如果情况适用：

  * 数据字典的状态、例程、事件和 UDF 缓存与 DDL 操作的状态一致，这意味着缓存被更
    新，以反映 DDL 操作是否成功完成或回滚。

  * DDL 操作中涉及的存储引擎方法不执行中间提交，存储引擎将自己注册为 DDL 事务的
    一部分。

  * 存储引擎支持 DDL 操作的重做和回滚，这是在 DDL 操作的 Post-DDL 阶段执行的。

* DDL 操作的可见行为是原子性的，这会改变一些 DDL 语句的行为。参见下面章节的
  Changes in DDL Statement Behavior。

  注意：
  DDL 语句（原子语句或其他语句）隐式地终止当前会话中任何活动的事务，就好像在执行
  语句之前已经执行了 `COMMIT` 一样。这意味着 DDL 语句不能在另一个事务中执行，不
  能在事务控制语句中执行比如 `START TRANSACTION ... COMMIT`，或与同一事务中的其
  他语句组合。

## Changes in DDL Statement Behavior

本节描述由于引入原子 DDL 支持而导致的 DDL 语句行为的改变。

* 如果所有指定的表使用的都是支持原子 DDL 的存储引擎，那么 `DROP TABLE` 操作完全
  是原子操作。该语句要么成功删除所有表，要么回滚。

  `DROP TABLE` fails with an error if a named table does not exist, and no changes are made, regardless of the storage engine. This change in behavior is demonstrated in the following example, where the `DROP TABLE` statement fails because a named table does not exist:
  如果指定的表不存在，且不做任何更改，无论存储引擎如何，`DROP TABLE` 都会出现错误。下面的示例演示了行为的这种变化，其中 `DROP TABLE` 语句失败，因为一个已命名的表不存在:
  ```
  mysql> CREATE TABLE t1 (c1 INT);

  mysql> DROP TABLE t1, t2;
  ERROR 1051 (42S02): Unknown table 'test.t2'

  mysql> SHOW TABLES;
  +----------------+
  | Tables_in_test |
  +----------------+
  | t1             |
  +----------------+
  ```

  Prior to the introduction of atomic DDL, `DROP TABLE` reports an error for the named table that does not exist but succeeds for the named table that does exist:
  在引入原子 DDL 之前，`DROP TABLE` 报告了一个不存在的已命名表的错误，但是成功的已命名表存在:
  ```
  mysql> CREATE TABLE t1 (c1 INT);
  
  mysql> DROP TABLE t1, t2;
  ERROR 1051 (42S02): Unknown table 'test.t2'
  
  mysql> SHOW TABLES;
  Empty set (0.00 sec)
  ```

  注意：
  由于这种行为的改变，MySQL 5.7 主机上部分完成的 `DROP TABLE` 语句在 MySQL 8.0 从服务器上复制时失败。为了避免这种失败场景，在 `DROP TABLE` 语句中使用 `IF EXISTS` 语法来防止对不存在的表发生错误。
  Due to this change in behavior, a partially completed `DROP TABLE` statement on a MySQL 5.7 master fails when replicated on a MySQL 8.0 slave. To avoid this failure scenario, use `IF EXISTS` syntax in `DROP TABLE` statements to prevent errors from occurring for tables that do not exist.

* `DROP DATABASE` is atomic if all tables use an atomic DDL-supported storage engine. The statement either drops all objects successfully or is rolled back. However, removal of the database directory from the file system occurs last and is not part of the atomic transaction. If removal of the database directory fails due to a file system error or server halt, the `DROP DATABASE` transaction is not rolled back.
* 如果所有表都使用原子ddl支持的存储引擎，那么 `DROP DATABASE` 就是原子的。该语句要么成功删除所有对象，要么回滚。但是，从文件系统中删除数据库目录是最后发生的，并且不是原子事务的一部分。如果由于文件系统错误或服务器停止而导致删除数据库目录失败，则不会回滚 `DROP DATABASE` 事务。

* For tables that do not use an atomic DDL-supported storage engine, table deletion occurs outside of the atomic `DROP TABLE` or `DROP DATABASE` transaction. Such table deletions are written to the binary log individually, which limits the discrepancy between the storage engine, data dictionary, and binary log to one table at most in the case of an interrupted `DROP TABLE` or `DROP DATABASE` operation. For operations that drop multiple tables, the tables that do not use an atomic DDL-supported storage engine are dropped before tables that do.
* 对于没有使用支持原子 DDL 的存储引擎的表，表删除发生在原子 `DROP TABLE` 或 `DROP DATABASE` 事务之外。这样的表删除被单独写入到二进制日志中，这将存储引擎、数据字典和二进制日志之间的差异限制为在中断的 `DROP TABLE` 或 `DROP DATABASE` 操作情况下最多只能有一个表。对于删除多个表的操作，不使用原子 DDL 支持的存储引擎的表会在删除表之前删除。

* `CREATE TABLE`, `ALTER TABLE`, `RENAME TABLE`, `TRUNCATE TABLE`, `CREATE TABLESPACE`, `DROP TABLESPACE` operations for tables that use an atomic DDL-supported storage engine are either fully committed or rolled back if the server halts during their operation. In earlier MySQL releases, interruption of these operations could cause discrepancies between the storage engine, data dictionary, and binary log, or leave behind orphan files. `RENAME TABLE` operations are only atomic if all named tables use an atomic DDL-supported storage engine.
* `CREATE TABLE`，`ALTER TABLE`，`RENAME TABLE`，`TRUNCATE TABLE`，`CREATE TABLESPACE`，`DROP TABLESPACE` 操作对于使用原子 DDL 支持的存储引擎的表来说，要么是完全提交的，要么是在服务器停止运行时回滚的。在早期的 MySQL 版本中，这些操作的中断可能会导致存储引擎、数据字典和二进制日志之间的差异，或者留下孤立文件。`RENAME TABLE` 操作只有在所有已命名表都使用原子 DDL 支持的存储引擎时才是原子操作。

* 如果一个指定的视图不存在，则 `DROP VIEW` 执行失败，执行语句不会对数据库做出任
  何改变。下面的例子演示了行为的变化，在这个例子中 `DROP VIEW` 语句执行失败，因
  为一个指定的视图不存在:
  ```
  mysql> CREATE VIEW test.viewA AS SELECT * FROM t;

  mysql> DROP VIEW test.viewA, test.viewB;
  ERROR 1051 (42S02): Unknown table 'test.viewB'

  mysql> SHOW FULL TABLES IN test WHERE TABLE_TYPE LIKE 'VIEW';
  +----------------+------------+
  | Tables_in_test | Table_type |
  +----------------+------------+
  | viewA | VIEW                |
  +----------------+------------+
  ```

  Prior to the introduction of atomic DDL, `DROP VIEW` returns an error for the named view that does not exist but succeeds for the named view that does exist:
  在引入原子 DDL 之前，`DROP VIEW` 会返回一个不存在的命名视图的错误，但是会成功返回存在的命名视图:
  ```
  mysql> CREATE VIEW test.viewA AS SELECT * FROM t;

  mysql> DROP VIEW test.viewA, test.viewB;
  ERROR 1051 (42S02): Unknown table 'test.viewB'

  mysql> SHOW FULL TABLES IN test WHERE TABLE_TYPE LIKE 'VIEW';
  Empty set (0.00 sec)
  ```

## Storage Engine Support

Currently, only the InnoDB storage engine supports atomic DDL. Storage engines that do not support atomic DDL are exempted from DDL atomicity. DDL operations involving exempted storage engines remain capable of introducing inconsistencies that can occur when operations are interrupted or only partially completed.
目前，只有 InnoDB 存储引擎支持原子 DDL 。不支持原子 DDL 的存储引擎不支持 DDL 原子性。涉及豁免存储引擎的 DDL 操作仍然能够引入当操作中断或仅部分完成时可能发生的不一致。

To support redo and rollback of DDL operations, InnoDB writes DDL logs to the `mysql.innodb_ddl_log` table, which is a hidden data dictionary table that resides in the `mysql.ibd` data dictionary tablespace.
为了支持 DDL 操作的重做和回滚，InnoDB 将 DDL 日志写入 `mysql.innodb_ddl_log` 表，它是一个隐藏的数据字典表，驻留在 `mysql.ibd` 数据字典表空间。

要查看在 DDL 操作期间写入 `mysql.innodb_ddl_log` 表的 DDL 日志，请启用
`innodb_print_ddl_logs` 配置选项。有关更多信息，请参见下面的章节 Viewing DDL
Logs。

Note
The redo logs for changes to the `mysql.innodb_ddl_log` table are flushed to disk immediately regardless of the `innodb_flush_log_at_trx_commit` setting.  Flushing the redo logs immediately avoids situations where data files are modified by DDL operations but the redo logs for changes to the `mysql.innodb_ddl_log` table resulting from those operations are not persisted to disk. Such a situation could cause errors during rollback or recovery.

The InnoDB storage engine executes DDL operations in phases. DDL operations such as `ALTER TABLE` may perform the Prepare and Perform phases multiple times prior to the Commit phase.

1. Prepare: Create the required objects and write the DDL logs to the `mysql.innodb_ddl_log` table.  The DDL logs define how to roll forward and roll back the DDL operation.

2. Perform: Perform the DDL operation. For example, perform a create routine for a `CREATE TABLE` operation.

3. Commit: Update the data dictionary and commit the data dictionary transaction.

4. Post-DDL: Replay and remove DDL logs from the `mysql.innodb_ddl_log` table. To ensure that rollback can be performed safely without introducing inconsistencies, file operations such as renaming or removing data files are performed in this final phase. This phase also removes dynamic metadata from the `mysql.innodb_dynamic_metadata` data dictionary table for `DROP TABLE`, `TRUNCATE TABLE`, and other DDL operations that rebuild the table.

DDL logs are replayed and removed from the `mysql.innodb_ddl_log` table during the Post-DDL phase, regardless of whether the transaction is committed or rolled back. DDL logs should only remain in the `mysql.innodb_ddl_log` table if the server is halted during a DDL operation. In this case, the DDL logs are replayed and removed after recovery.

In a recovery situation, a DDL transaction may be committed or rolled back when the server is restarted. If the data dictionary transaction that was performed during the Commit phase of a DDL operation is present in the redo log and binary log, the operation is considered successful and is rolled forward. Otherwise, the incomplete data dictionary transaction is rolled back when InnoDB replays data dictionary redo logs, and the DDL transaction is rolled back.

## Viewing DDL Logs

To view DDL logs that are written to the `mysql.innodb_ddl_log` data dictionary table during atomic DDL operations that involve the InnoDB storage engine, enable `innodb_print_ddl_logs` to have MySQL write the DDL logs to stderr. Depending on the host operating system and MySQL configuration, stderr may be the error log, terminal, or console window. See Section 5.4.2.2, “Default Error Log Destination Configuration”.

InnoDB writes DDL logs to the `mysql.innodb_ddl_log` table to support redo and rollback of DDL operations. The `mysql.innodb_ddl_log` table is a hidden data dictionary table that resides in the `mysql.ibd` data dictionary tablespace. Like other hidden data dictionary tables, the `mysql.innodb_ddl_log` table cannot be accessed directly in non-debug versions of MySQL.  (See Section 14.1, “Data Dictionary Schema”.) The structure of the `mysql.innodb_ddl_log` table corresponds to this definition:
```
CREATE TABLE mysql.innodb_ddl_log (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
    thread_id BIGINT UNSIGNED NOT NULL,
    type INT UNSIGNED NOT NULL,
    space_id INT UNSIGNED,
    page_no INT UNSIGNED,
    index_id BIGINT UNSIGNED,
    table_id BIGINT UNSIGNED,
    old_file_path VARCHAR(512) COLLATE UTF8_BIN,
    new_file_path VARCHAR(512) COLLATE UTF8_BIN,
    KEY(thread_id)
);
```
* id: A unique identifier for a DDL log record.
* thread_id: Each DDL log record is assigned a thread_id, which is used to replay and remove DDL logs that belong to a particular DDL transaction. DDL transactions that involve multiple data file operations generate multiple DDL log records.
* type: The DDL operation type. Types include FREE (drop an index tree), DELETE (delete a file), RENAME (rename a file), or DROP (drop metadata from the mysql.innodb_dynamic_metadata data dictionary table).
* space_id: The tablespace ID.
* page_no: A page that contains allocation information; an index tree root page, for example.
* index_id: The index ID.
* table_id: The table ID.
* old_file_path: The old tablespace file path. Used by DDL operations that create or drop tablespace files; also used by DDL operations that rename a tablespace.
* new_file_path: The new tablespace file path. Used by DDL operations that rename tablespace files.

This example demonstrates enabling `innodb_print_ddl_logs` to view DDL logs written to strderr for a `CREATE TABLE` operation.
```
mysql> SET GLOBAL innodb_print_ddl_logs=1;

mysql> CREATE TABLE t1 (c1 INT) ENGINE = InnoDB;
[Note] [000000] InnoDB: DDL log insert : [DDL record: DELETE SPACE, id=18, thread_id=7,
space_id=5, old_file_path=./test/t1.ibd]
[Note] [000000] InnoDB: DDL log delete : by id 18
[Note] [000000] InnoDB: DDL log insert : [DDL record: REMOVE CACHE, id=19, thread_id=7,
table_id=1058, new_file_path=test/t1]
[Note] [000000] InnoDB: DDL log delete : by id 19
[Note] [000000] InnoDB: DDL log insert : [DDL record: FREE, id=20, thread_id=7,
space_id=5, index_id=132, page_no=4]
[Note] [000000] InnoDB: DDL log delete : by id 20
[Note] [000000] InnoDB: DDL log post ddl : begin for thread id : 7
[Note] [000000] InnoDB: DDL log post ddl : end for thread id : 7
```
