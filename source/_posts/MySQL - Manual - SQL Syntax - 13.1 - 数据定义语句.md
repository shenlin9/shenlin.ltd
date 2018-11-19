---
title: MySQL - Manual - SQL Syntax - 13.1 - 数据定义语句
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
---

MySQL 数据定义语句

<!--more-->

## 13.1.1 Atomic Data Definition Statement Support

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

### Supported DDL Statements

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

### Atomic DDL Characteristics

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

### Changes in DDL Statement Behavior

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

### Storage Engine Support

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

### Viewing DDL Logs

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

## 13.1.2 ALTER DATABASE Syntax

```
ALTER {DATABASE | SCHEMA} [db_name]
alter_specification ...
alter_specification:

[DEFAULT] CHARACTER SET [=] charset_name
| [DEFAULT] COLLATE [=] collation_name
```

`ALTER DATABASE` enables you to change the overall characteristics of a database. These characteristics are stored in the data dictionary. To use `ALTER DATABASE`, you need the `ALTER` privilege on the database. `ALTER SCHEMA` is a synonym for `ALTER DATABASE`.

The database name can be omitted from the first syntax, in which case the statement applies to the default database.

### National Language Characteristics

The `CHARACTER SET` clause changes the default database character set. The `COLLATE` clause changes the default database collation. Chapter 10, Character Sets, Collations, Unicode, discusses character set and collation names.

You can see what character sets and collations are available using, respectively, the `SHOW CHARACTER SET` and `SHOW COLLATION` statements. See Section 13.7.6.3, “SHOW CHARACTER SET Syntax”, and Section 13.7.6.4, “SHOW COLLATION Syntax”, for more information.

If you change the default character set or collation for a database, stored routines that use the database defaults must be dropped and recreated so that they use the new defaults. (In a stored routine, variables with character data types use the database defaults if the character set or collation are not specified explicitly. See Section 13.1.15, “CREATE PROCEDURE and CREATE FUNCTION Syntax”.)

## 13.1.3 ALTER EVENT Syntax

```
ALTER
    [DEFINER = { user | CURRENT_USER }]
    EVENT event_name
    [ON SCHEDULE schedule]
    [ON COMPLETION [NOT] PRESERVE]
    [RENAME TO new_event_name]
    [ENABLE | DISABLE | DISABLE ON SLAVE]
    [COMMENT 'string']
    [DO event_body]
```

The `ALTER EVENT` statement changes one or more of the characteristics of an existing event without the need to drop and recreate it. The syntax for each of the `DEFINER`, `ON SCHEDULE`, `ON COMPLETION`, `COMMENT`, `ENABLE / DISABLE`, and `DO` clauses is exactly the same as when used with `CREATE EVENT`.  (See Section 13.1.12, “CREATE EVENT Syntax”.)

Any user can alter an event defined on a database for which that user has the `EVENT` privilege. When a user executes a successful `ALTER EVENT` statement, that user becomes the definer for the affected event.

`ALTER EVENT` works only with an existing event:
```
mysql> ALTER EVENT no_such_event
> ON SCHEDULE
> EVERY '2:3' DAY_HOUR;
ERROR 1517 (HY000): Unknown event 'no_such_event'
```

In each of the following examples, assume that the event named `myevent` is defined as shown here:
```
CREATE EVENT myevent
ON SCHEDULE
EVERY 6 HOUR
COMMENT 'A sample comment.'
DO
UPDATE myschema.mytable SET mycol = mycol + 1;
```

The following statement changes the schedule for `myevent` from once every six hours starting immediately to once every twelve hours, starting four hours from the time the statement is run:
```
ALTER EVENT myevent
ON SCHEDULE
EVERY 12 HOUR
STARTS CURRENT_TIMESTAMP + INTERVAL 4 HOUR;
```

It is possible to change multiple characteristics of an event in a single statement. This example changes the SQL statement executed by `myevent` to one that deletes all records from `mytable`; it also changes the schedule for the event such that it executes once, one day after this `ALTER EVENT` statement is run.
```
ALTER EVENT myevent
    ON SCHEDULE
        AT CURRENT_TIMESTAMP + INTERVAL 1 DAY
    DO
        TRUNCATE TABLE myschema.mytable;
```

Specify the options in an `ALTER EVENT` statement only for those characteristics that you want to change; omitted options keep their existing values. This includes any default values for `CREATE EVENT` such as `ENABLE`.

To disable `myevent`, use this `ALTER EVENT` statement:
```
ALTER EVENT myevent
    DISABLE;
```

The `ON SCHEDULE` clause may use expressions involving built-in MySQL functions and user variables to obtain any of the timestamp or interval values which it contains. You cannot use stored routines or user-defined functions in such expressions, and you cannot use any table references; however, you can use `SELECT FROM DUAL`. This is true for both `ALTER EVENT` and `CREATE EVENT` statements.  References to stored routines, user-defined functions, and tables in such cases are specifically not permitted, and fail with an error (see Bug #22830).

Although an `ALTER EVENT` statement that contains another `ALTER EVENT` statement in its DO clause appears to succeed, when the server attempts to execute the resulting scheduled event, the execution fails with an error.

To rename an event, use the `ALTER EVENT` statement's `RENAME TO` clause. This statement renames the event `myevent` to `yourevent`:
```
ALTER EVENT myevent
    RENAME TO yourevent;
```

You can also move an event to a different database using `ALTER EVENT ... RENAME TO ...` and `db_name.event_name` notation, as shown here:
```
ALTER EVENT olddb.myevent
    RENAME TO newdb.myevent;
```

To execute the previous statement, the user executing it must have the `EVENT` privilege on both the `olddb` and `newdb` databases.

    Note
    There is no RENAME EVENT statement.

The value `DISABLE ON SLAVE` is used on a replication slave instead of `ENABLE` or `DISABLE` to indicate an event that was created on the master and replicated to the slave, but that is not executed on the slave.  Normally, `DISABLE ON SLAVE` is set automatically as required; however, there are some circumstances under which you may want or need to change it manually. See Section 17.4.1.16, “Replication of Invoked Features”, for more information.

## 13.1.4 ALTER FUNCTION Syntax

```
ALTER FUNCTION func_name [characteristic ...]

characteristic:
    COMMENT 'string'
    | LANGUAGE SQL
    | { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
    | SQL SECURITY { DEFINER | INVOKER }
```

This statement can be used to change the characteristics of a stored function. More than one change may be specified in an `ALTER FUNCTION` statement. However, you cannot change the parameters or body of a stored function using this statement; to make such changes, you must drop and re-create the function using `DROP FUNCTION` and `CREATE FUNCTION`.

You must have the `ALTER ROUTINE` privilege for the function. (That privilege is granted automatically to the function creator.) If binary logging is enabled, the `ALTER FUNCTION` statement might also require the `SUPER` privilege, as described in Section 23.7, “Binary Logging of Stored Programs”.

## 13.1.5 ALTER INSTANCE Syntax

```
ALTER INSTANCE ROTATE INNODB MASTER KEY
```

`ALTER INSTANCE` defines actions applicable to a MySQL server instance.

The `ALTER INSTANCE ROTATE INNODB MASTER KEY` statement is used to rotate the master
encryption key used for InnoDB tablespace encryption. A keyring plugin must be loaded to use this
statement. By default, the MySQL server loads the `keyring_file` plugin. Key rotation requires the
`ENCRYPTION_KEY_ADMIN` or `SUPER` privilege.

`ALTER INSTANCE ROTATE INNODB MASTER KEY` supports concurrent DML. However, it cannot be run
concurrently with `CREATE TABLE ... ENCRYPTION` or `ALTER TABLE ... ENCRYPTION` operations,
and locks are taken to prevent conflicts that could arise from concurrent execution of these statements. If
one of the conflicting statements is running, it must complete before another can proceed.

`ALTER INSTANCE` actions are written to the binary log so that they can be executed on replicated servers.

For additional `ALTER INSTANCE ROTATE INNODB MASTER KEY` usage information, see Section 15.7.11, “InnoDB Tablespace Encryption”. For information about the keyring_file plugin, see Section 6.5.4, “The MySQL Keyring”.

## 13.1.6 ALTER PROCEDURE Syntax

```
ALTER PROCEDURE proc_name [characteristic ...]

characteristic:
    COMMENT 'string'
    | LANGUAGE SQL
    | { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
    | SQL SECURITY { DEFINER | INVOKER }
```

This statement can be used to change the characteristics of a stored procedure. More than one change
may be specified in an `ALTER PROCEDURE` statement. However, you cannot change the parameters or
body of a stored procedure using this statement; to make such changes, you must drop and re-create the
procedure using `DROP PROCEDURE` and `CREATE PROCEDURE`.

You must have the `ALTER ROUTINE` privilege for the procedure. By default, that privilege is
granted automatically to the procedure creator. This behavior can be changed by disabling the
`automatic_sp_privileges` system variable. See Section 23.2.2, “Stored Routines and MySQL
Privileges”.


## 13.1.7 ALTER SERVER Syntax

```
ALTER SERVER server_name
    OPTIONS (option [, option] ...)
```

Alters the server information for `server_name`, adjusting any of the options permitted in the `CREATE
SERVER` statement. The corresponding fields in the `mysql.servers` table are updated accordingly. This
statement requires the `SUPER` privilege.

For example, to update the `USER` option:
```
ALTER SERVER s OPTIONS (USER 'sally');
```

`ALTER SERVER` causes an implicit commit. See Section 13.3.3, “Statements That Cause an Implicit
Commit”.

`ALTER SERVER` is not written to the binary log, regardless of the logging format that is in use.

## 13.1.8 ALTER TABLE Syntax

```
ALTER TABLE tbl_name
    [alter_specification [, alter_specification] ...]
    [partition_options]

alter_specification:
    table_options
        | ADD [COLUMN] col_name column_definition
            [FIRST | AFTER col_name]
        | ADD [COLUMN] (col_name column_definition,...)
        | ADD {INDEX|KEY} [index_name]
            [index_type] (key_part,...) [index_option] ...
        | ADD [CONSTRAINT [symbol]] PRIMARY KEY
            [index_type] (key_part,...) [index_option] ...
        | ADD [CONSTRAINT [symbol]]
            UNIQUE [INDEX|KEY] [index_name]
            [index_type] (key_part,...) [index_option] ...
        | ADD FULLTEXT [INDEX|KEY] [index_name]
            (key_part,...) [index_option] ...
        | ADD SPATIAL [INDEX|KEY] [index_name]
            (key_part,...) [index_option] ...
        | ADD [CONSTRAINT [symbol]]
    FOREIGN KEY [index_name] (col_name,...)
    reference_definition
        | ALGORITHM [=] {DEFAULT|INSTANT|INPLACE|COPY}
        | ALTER [COLUMN] col_name {SET DEFAULT literal | DROP DEFAULT}
        | ALTER INDEX index_name {VISIBLE | INVISIBLE}
        | CHANGE [COLUMN] old_col_name new_col_name column_definition
            [FIRST|AFTER col_name]
        | [DEFAULT] CHARACTER SET [=] charset_name [COLLATE [=] collation_name]
        | CONVERT TO CHARACTER SET charset_name [COLLATE collation_name]
        | {DISABLE|ENABLE} KEYS
        | {DISCARD|IMPORT} TABLESPACE
        | DROP [COLUMN] col_name
        | DROP {INDEX|KEY} index_name
        | DROP PRIMARY KEY
        | DROP FOREIGN KEY fk_symbol
        | FORCE
        | LOCK [=] {DEFAULT|NONE|SHARED|EXCLUSIVE}
        | MODIFY [COLUMN] col_name column_definition
            [FIRST | AFTER col_name]
        | ORDER BY col_name [, col_name] ...
        | RENAME COLUMN old_col_name TO new_col_name
        | RENAME {INDEX|KEY} old_index_name TO new_index_name
        | RENAME [TO|AS] new_tbl_name
        | {WITHOUT|WITH} VALIDATION
        | ADD PARTITION (partition_definition)
        | DROP PARTITION partition_names
        | DISCARD PARTITION {partition_names | ALL} TABLESPACE
        | IMPORT PARTITION {partition_names | ALL} TABLESPACE
        | TRUNCATE PARTITION {partition_names | ALL}
        | COALESCE PARTITION number
        | REORGANIZE PARTITION partition_names INTO (partition_definitions)
        | EXCHANGE PARTITION partition_name WITH TABLE tbl_name [{WITH|WITHOUT} VALIDATION]
        | ANALYZE PARTITION {partition_names | ALL}
        | CHECK PARTITION {partition_names | ALL}
        | OPTIMIZE PARTITION {partition_names | ALL}
        | REBUILD PARTITION {partition_names | ALL}
        | REPAIR PARTITION {partition_names | ALL}
        | REMOVE PARTITIONING
        | UPGRADE PARTITIONING

key_part:
    {col_name [(length)] | (expr)} [ASC | DESC]

index_type:
    USING {BTREE | HASH}

index_option:
    KEY_BLOCK_SIZE [=] value
    | index_type
    | WITH PARSER parser_name
    | COMMENT 'string'
    | {VISIBLE | INVISIBLE}

table_options:
    table_option [[,] table_option] ...

table_option:
    AUTO_INCREMENT [=] value
    | AVG_ROW_LENGTH [=] value
    | [DEFAULT] CHARACTER SET [=] charset_name
    | CHECKSUM [=] {0 | 1}
    | [DEFAULT] COLLATE [=] collation_name
    | COMMENT [=] 'string'
    | COMPRESSION [=] {'ZLIB'|'LZ4'|'NONE'}
    | CONNECTION [=] 'connect_string'
    | {DATA|INDEX} DIRECTORY [=] 'absolute path to directory'
    | DELAY_KEY_WRITE [=] {0 | 1}
    | ENCRYPTION [=] {'Y' | 'N'}
    | ENGINE [=] engine_name
    | INSERT_METHOD [=] { NO | FIRST | LAST }
    | KEY_BLOCK_SIZE [=] value
    | MAX_ROWS [=] value
    | MIN_ROWS [=] value
    | PACK_KEYS [=] {0 | 1 | DEFAULT}
    | PASSWORD [=] 'string'
    | ROW_FORMAT [=] {DEFAULT|DYNAMIC|FIXED|COMPRESSED|REDUNDANT|COMPACT}
    | STATS_AUTO_RECALC [=] {DEFAULT|0|1}
    | STATS_PERSISTENT [=] {DEFAULT|0|1}
    | STATS_SAMPLE_PAGES [=] value
    | TABLESPACE tablespace_name
    | UNION [=] (tbl_name[,tbl_name]...)

partition_options:
    (see CREATE TABLE options)
```

ALTER TABLE changes the structure of a table. For example, you can add or delete columns, create or
destroy indexes, change the type of existing columns, or rename columns or the table itself. You can also
change characteristics such as the storage engine used for the table or the table comment.

• To use ALTER TABLE, you need ALTER, CREATE, and INSERT privileges for the table. Renaming a
table requires ALTER and DROP on the old table, ALTER, CREATE, and INSERT on the new table.
• Following the table name, specify the alterations to be made. If none are given, ALTER TABLE does
nothing.
• The syntax for many of the permissible alterations is similar to clauses of the CREATE TABLE statement.
column_definition clauses use the same syntax for ADD and CHANGE as for CREATE TABLE. For
more information, see Section 13.1.18, “CREATE TABLE Syntax”.
• The word COLUMN is optional and can be omitted, except for RENAME COLUMN (to distinguish a column-
renaming operation from the RENAME table-renaming operation).
• Multiple ADD, ALTER, DROP, and CHANGE clauses are permitted in a single ALTER TABLE statement,
separated by commas. This is a MySQL extension to standard SQL, which permits only one of each
clause per ALTER TABLE statement. For example, to drop multiple columns in a single statement, do
this:
```
ALTER TABLE t2 DROP COLUMN c, DROP COLUMN d;
```
• If a storage engine does not support an attempted ALTER TABLE operation, a warning may result. Such
warnings can be displayed with SHOW WARNINGS. See Section 13.7.6.40, “SHOW WARNINGS Syntax”.
For information on troubleshooting ALTER TABLE, see Section B.5.6.1, “Problems with ALTER TABLE”.
• For information about generated columns, see Section 13.1.8.2, “ALTER TABLE and Generated
Columns”.
• For usage examples, see Section 13.1.8.3, “ALTER TABLE Examples”.
• With the mysql_info() C API function, you can find out how many rows were copied by ALTER
TABLE. See Section 27.7.7.36, “mysql_info()”.

There are several additional aspects to the ALTER TABLE statement, described under the following topics
in this section:

• Table Options
• Performance and Space Requirements
• Concurrency Control
• Adding and Dropping Columns
• Renaming, Redefining, and Reordering Columns
• Primary Keys and Indexes
• Foreign Keys
• Changing the Character Set
• Discarding and Importing InnoDB Tablespaces
• Row Order for MyISAM Tables
• Partitioning Options

### Table Options



### Performance and Space Requirements



### Concurrency Control



### Adding and Dropping Columns



### Renaming, Redefining, and Reordering Columns



### Primary Keys and Indexes



### Foreign Keys



### Changing the Character Set



### Discarding and Importing InnoDB Tablespaces



### Row Order for MyISAM Tables



### Partitioning Options




### 13.1.8.1 ALTER TABLE Partition Operations

Partitioning-related clauses for ALTER TABLE can be used with partitioned tables for repartitioning, to add, drop, discard, import, merge, and split partitions, and to perform partitioning maintenance.

* Simply using a `partition_options` clause with `ALTER TABLE` on a partitioned table repartitions the table according to the partitioning scheme defined by the `partition_options`. This clause always begins with `PARTITION BY`, and follows the same syntax and other rules as apply to the `partition_options` clause for `CREATE TABLE` (for more detailed information, see Section 13.1.18,“CREATE TABLE Syntax”), and can also be used to partition an existing table that is not already partitioned. For example, consider a (nonpartitioned) table defined as shown here:
  ```
  CREATE TABLE t1 (
      id INT,
      year_col INT
  );
  ```

  This table can be partitioned by HASH, using the id column as the partitioning key, into 8 partitions by means of this statement:
  ```
  ALTER TABLE t1
      PARTITION BY HASH(id)
      PARTITIONS 8;
  ```

  MySQL supports an ALGORITHM option with [SUB]PARTITION BY [LINEAR] KEY. ALGORITHM=1 causes the server to use the same key-hashing functions as MySQL 5.1 when computing the placement of rows in partitions; ALGORITHM=2 means that the server employs the key-hashing functions implemented and used by default for new KEY partitioned tables in MySQL 5.5 and later. (Partitioned tables created with the key-hashing functions employed in MySQL 5.5 and later cannot be used by a MySQL 5.1 server.) Not specifying the option has the same effect as using ALGORITHM=2. This option is intended for use chiefly when upgrading or downgrading [LINEAR] KEY partitioned tables between MySQL 5.1 and later MySQL versions, or for creating tables partitioned by KEY or LINEAR KEY on a MySQL 5.5 or later server which can be used on a MySQL 5.1 server.

  The table that results from using an ALTER TABLE ... PARTITION BY statement must follow the same rules as one created using CREATE TABLE ... PARTITION BY. This includes the rules governing the relationship between any unique keys (including any primary key) that the table might have, and the column or columns used in the partitioning expression, as discussed in Section 22.6.1,“Partitioning Keys, Primary Keys, and Unique Keys”. The CREATE TABLE ... PARTITION BY rules for specifying the number of partitions also apply to ALTER TABLE ... PARTITION BY.

  The partition_definition clause for `ALTER TABLE ADD PARTITION` supports the same options as the clause of the same name for the `CREATE TABLE` statement. (See Section 13.1.18, “CREATE TABLE Syntax”, for the syntax and description.) Suppose that you have the partitioned table created as shown here:
  ```
  CREATE TABLE t1 (
      id INT,
      year_col INT
  )
      PARTITION BY RANGE (year_col) (
      PARTITION p0 VALUES LESS THAN (1991),
      PARTITION p1 VALUES LESS THAN (1995),
      PARTITION p2 VALUES LESS THAN (1999)
  );
  ```

  You can add a new partition `p3` to this table for storing values less than 2002 as follows:

  `DROP PARTITION` can be used to drop one or more `RANGE` or `LIST` partitions. This statement cannot be used with `HASH` or `KEY` partitions; instead, use `COALESCE PARTITION` (see later in this section). Any data that was stored in the dropped partitions named in the `partition_names` list is discarded. For example, given the table `t1` defined previously, you can drop the partitions named `p0` and `p1` as shown here:
  ```
  ALTER TABLE t1 DROP PARTITION p0, p1;
  ```

  `ADD PARTITION` and `DROP PARTITION` do not currently support `IF [NOT] EXISTS`.

  The `DISCARD PARTITION ... TABLESPACE` and `IMPORT PARTITION ... TABLESPACE` options extend the Transportable Tablespace feature to individual InnoDB table partitions. Each InnoDB table partition has its own tablespace file (`.idb` file). The Transportable Tablespace feature makes it easy to copy the tablespaces from a running MySQL server instance to another running instance, or to perform a restore on the same instance. Both options take a comma-separated list of one or more partition names.

For example:
```
ALTER TABLE t1 DISCARD PARTITION p2, p3 TABLESPACE;
ALTER TABLE t1 IMPORT PARTITION p2, p3 TABLESPACE;
```
When running `DISCARD PARTITION ... TABLESPACE` and `IMPORT PARTITION ...  TABLESPACE` on subpartitioned tables, both partition and subpartition names are allowed. When a partition name is specified, subpartitions of that partition are included.

The Transportable Tablespace feature also supports copying or restoring partitioned InnoDB tables (all partitions at once). For additional information, see Section 15.7.6, “Copying File-Per-Table Tablespaces to Another Instance”, as well as, Section 15.7.6.1, “Transportable Tablespace Examples”.

Renames of partitioned tables are supported. You can rename individual partitions indirectly using `ALTER TABLE ... REORGANIZE PARTITION`; however, this operation copies the partition's data.

To delete rows from selected partitions, use the `TRUNCATE PARTITION` option. This option takes a list of one or more comma-separated partition names. Consider the table `t1` created by this statement:
```
CREATE TABLE t1 (
    id INT,
    year_col INT
)
PARTITION BY RANGE (year_col) (
    PARTITION p0 VALUES LESS THAN (1991),
    PARTITION p1 VALUES LESS THAN (1995),
    PARTITION p2 VALUES LESS THAN (1999),
    PARTITION p3 VALUES LESS THAN (2003),
    PARTITION p4 VALUES LESS THAN (2007)
);
```

To delete all rows from partition `p0`, use the following statement:
```
ALTER TABLE t1 TRUNCATE PARTITION p0;
```

The statement just shown has the same effect as the following `DELETE` statement:
```
DELETE FROM t1 WHERE year_col < 1991;
```

When truncating multiple partitions, the partitions do not have to be contiguous: This can greatly simplify
delete operations on partitioned tables that would otherwise require very complex `WHERE` conditions if
done with `DELETE` statements. For example, this statement deletes all rows from partitions `p1` and `p3`:
```
ALTER TABLE t1 TRUNCATE PARTITION p1, p3;
```

An equivalent `DELETE` statement is shown here:
```
DELETE FROM t1 WHERE
    (year_col >= 1991 AND year_col < 1995)
OR
    (year_col >= 2003 AND year_col < 2007);
```

If you use the `ALL` keyword in place of the list of partition names, the statement acts on all table
partitions.

`TRUNCATE PARTITION` merely deletes rows; it does not alter the definition of the table itself, or of any
of its partitions.

To verify that the rows were dropped, check the `INFORMATION_SCHEMA.PARTITIONS` table, using a
query such as this one:
```
SELECT PARTITION_NAME, TABLE_ROWS
FROM INFORMATION_SCHEMA.PARTITIONS
WHERE TABLE_NAME = 't1';
```

`COALESCE PARTITION` can be used with a table that is partitioned by `HASH` or `KEY` to reduce the
number of partitions by number. Suppose that you have created table `t2` as follows:
```
CREATE TABLE t2 (
    name VARCHAR (30),
    started DATE
)
PARTITION BY HASH( YEAR(started) )
PARTITIONS 6;
```

To reduce the number of partitions used by t2 from 6 to 4, use the following statement:
```
ALTER TABLE t2 COALESCE PARTITION 2;
```

The data contained in the last number partitions will be merged into the remaining partitions. In this case, partitions 4 and 5 will be merged into the first 4 partitions (the partitions numbered 0, 1, 2, and 3).

To change some but not all the partitions used by a partitioned table, you can use `REORGANIZE PARTITION`. This statement can be used in several ways:

* To merge a set of partitions into a single partition. This is done by naming several partitions in the `partition_names` list and supplying a single definition for `partition_definition`.

* To split an existing partition into several partitions. Accomplish this by naming a single partition for `partition_names` and providing multiple `partition_definitions`.

* To change the ranges for a subset of partitions defined using `VALUES LESS THAN` or the value lists for a subset of partitions defined using `VALUES IN`.

* To move a partition from one tablespace to another. For an example, see `15.7.10 InnoDB General Tablespaces - Moving Table Partitions Between Tablespaces Using ALTER TABLE`.

    Note
    For partitions that have not been explicitly named, MySQL automatically provides
    the default names p0, p1, p2, and so on. The same is true with regard to
    subpartitions.

For more detailed information about and examples of ALTER TABLE ... REORGANIZE PARTITION
statements, see Section 22.3.1, “Management of RANGE and LIST Partitions”.

• To exchange a table partition or subpartition with a table, use the ALTER TABLE ... EXCHANGE
  PARTITION statement—that is, to move any existing rows in the partition or subpartition to the
  nonpartitioned table, and any existing rows in the nonpartitioned table to the table partition or
  subpartition.
  For usage information and examples, see Section 22.3.3, “Exchanging Partitions and Subpartitions with
  Tables”.
• Several options provide partition maintenance and repair functionality analogous to that implemented
  for nonpartitioned tables by statements such as CHECK TABLE and REPAIR TABLE (which are
  also supported for partitioned tables; for more information, see Section 13.7.3, “Table Maintenance
  Statements”). These include ANALYZE PARTITION, CHECK PARTITION, OPTIMIZE PARTITION,
  REBUILD PARTITION, and REPAIR PARTITION. Each of these options takes a partition_names
  clause consisting of one or more names of partitions, separated by commas. The partitions must already
  exist in the target table. You can also use the ALL keyword in place of partition_names, in which
  case the statement acts on all table partitions. For more information and examples, see Section 22.3.4,
  “Maintenance of Partitions”.
  InnoDB does not currently support per-partition optimization; ALTER TABLE ... OPTIMIZE
  PARTITION causes the entire table to rebuilt and analyzed, and an appropriate warning to be issued.
  (Bug #11751825, Bug #42822) To work around this problem, use ALTER TABLE ... REBUILD
  PARTITION and ALTER TABLE ... ANALYZE PARTITION instead.
  The ANALYZE PARTITION, CHECK PARTITION, OPTIMIZE PARTITION, and REPAIR PARTITION
  options are not supported for tables which are not partitioned.
• REMOVE PARTITIONING enables you to remove a table's partitioning without otherwise affecting the
  table or its data. This option can be combined with other ALTER TABLE options such as those used to
  add, drop, or rename columns or indexes.
• Using the ENGINE option with ALTER TABLE changes the storage engine used by the table without
  affecting the partitioning. The target storage engine must provide its own partitioning handler. Only the
  InnoDB and NDB storage engines have native partitioning handlers; NDB is not currently supported in
  MySQL 8.0.

It is possible for an ALTER TABLE statement to contain a PARTITION BY or REMOVE PARTITIONING
clause in an addition to other alter specifications, but the PARTITION BY or REMOVE PARTITIONING
clause must be specified last after any other specifications.

The ADD PARTITION, DROP PARTITION, COALESCE PARTITION, REORGANIZE PARTITION,
ANALYZE PARTITION, CHECK PARTITION, and REPAIR PARTITION options cannot be combined with
other alter specifications in a single ALTER TABLE, since the options just listed act on individual partitions.
For more information, see Section 13.1.8.1, “ALTER TABLE Partition Operations”.

Only a single instance of any one of the following options can be used in a given ALTER TABLE
statement: PARTITION BY, ADD PARTITION, DROP PARTITION, TRUNCATE PARTITION, EXCHANGE
PARTITION, REORGANIZE PARTITION, or COALESCE PARTITION, ANALYZE PARTITION, CHECK
PARTITION, OPTIMIZE PARTITION, REBUILD PARTITION, REMOVE PARTITIONING.

For example, the following two statements are invalid:
```
ALTER TABLE t1 ANALYZE PARTITION p1, ANALYZE PARTITION p2;
ALTER TABLE t1 ANALYZE PARTITION p1, CHECK PARTITION p2;
```
In the first case, you can analyze partitions p1 and p2 of table t1 concurrently using a single statement
with a single ANALYZE PARTITION option that lists both of the partitions to be analyzed, like this:
```
ALTER TABLE t1 ANALYZE PARTITION p1, p2;
```
In the second case, it is not possible to perform ANALYZE and CHECK operations on different partitions of
the same table concurrently. Instead, you must issue two separate statements, like this:
```
ALTER TABLE t1 ANALYZE PARTITION p1;
ALTER TABLE t1 CHECK PARTITION p2;
```
REBUILD operations are currently unsupported for subpartitions. The REBUILD keyword is expressly
disallowed with subpartitions, and causes ALTER TABLE to fail with an error if so used.
CHECK PARTITION and REPAIR PARTITION operations fail when the partition to be checked or
repaired contains any duplicate key errors.

For more information about these statements, see Section 22.3.4, “Maintenance of Partitions”.

### 13.1.8.2 ALTER TABLE and Generated Columns



### 13.1.8.3 ALTER TABLE Examples



## 13.1.9 ALTER TABLESPACE Syntax

```
ALTER TABLESPACE tablespace_name
RENAME TO tablespace_name
[ENGINE [=] engine_name]
```

This statement can be used to rename an InnoDB general tablespace.

The `CREATE TABLESPACE` privilege is required to rename a general tablespace.

The `ENGINE` clause, which specifies the storage engine used by the tablespace, is deprecated and will be
removed in a future release. The tablespace storage engine is known by the data dictionary, making the
`ENGINE` clause obsolete. If the storage engine is specified, it must match the tablespace storage engine
defined in the data dictionary.

`RENAME TO` operations are implicitly performed in autocommit mode, regardless of the autocommit
setting.

A `RENAME TO` operation cannot be performed while `LOCK TABLES` or `FLUSH TABLES WITH READ LOCK`
is in effect for tables that reside in the tablespace.

Exclusive metadata locks are taken on tables that reside in a general tablespace while the tablespace is renamed, which prevents concurrent DDL. Concurrent DML is supported.

## 13.1.10 ALTER VIEW Syntax

```
ALTER
    [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    [DEFINER = { user | CURRENT_USER }]
    [SQL SECURITY { DEFINER | INVOKER }]
VIEW view_name [(column_list)]
AS select_statement
    [WITH [CASCADED | LOCAL] CHECK OPTION]
```

This statement changes the definition of a view, which must exist. The syntax is similar to that for `CREATE VIEW` see Section 13.1.21, “CREATE VIEW Syntax”). This statement requires the `CREATE VIEW` and `DROP` privileges for the view, and some privilege for each column referred to in the `SELECT` statement.  `ALTER VIEW` is permitted only to the definer or users with the `SET_USER_ID` or `SUPER` privilege.

## 13.1.11 CREATE DATABASE Syntax

```
CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] db_name
    [create_specification] ...

create_specification:
[DEFAULT] CHARACTER SET [=] charset_name
| [DEFAULT] COLLATE [=] collation_name
```

CREATE DATABASE creates a database with the given name. To use this statement, you need the CREATE
privilege for the database. CREATE SCHEMA is a synonym for CREATE DATABASE.

An error occurs if the database exists and you did not specify IF NOT EXISTS.

CREATE DATABASE is not permitted within a session that has an active LOCK TABLES statement.

create_specification options specify database characteristics. Database characteristics are stored
in the data dictionary. The CHARACTER SET clause specifies the default database character set. The
COLLATE clause specifies the default database collation. Chapter 10, Character Sets, Collations, Unicode,
discusses character set and collation names.

A database in MySQL is implemented as a directory containing files that correspond to tables in the
database. Because there are no tables in a database when it is initially created, the CREATE DATABASE
statement creates only a directory under the MySQL data directory. Rules for permissible database
names are given in Section 9.2, “Schema Object Names”. If a database name contains special characters,
the name for the database directory contains encoded versions of those characters as described in
Section 9.2.3, “Mapping of Identifiers to File Names”.

Creating a database directory by manually creating a directory under the data directory (for example, with
mkdir) is temporarily unsupported in MySQL 8.0.0.

You can also use the mysqladmin program to create databases. See Section 4.5.2, “mysqladmin —
Client for Administering a MySQL Server”.

## 13.1.12 CREATE EVENT Syntax

```
CREATE
    [DEFINER = { user | CURRENT_USER }]
    EVENT
    [IF NOT EXISTS]
    event_name
    ON SCHEDULE schedule
    [ON COMPLETION [NOT] PRESERVE]
    [ENABLE | DISABLE | DISABLE ON SLAVE]
    [COMMENT 'string']
    DO event_body;

schedule:
    AT timestamp [+ INTERVAL interval] ...
    | EVERY interval
    [STARTS timestamp [+ INTERVAL interval] ...]
    [ENDS timestamp [+ INTERVAL interval] ...]

interval:
    quantity {YEAR | QUARTER | MONTH | DAY | HOUR | MINUTE |
    WEEK | SECOND | YEAR_MONTH | DAY_HOUR | DAY_MINUTE |
    DAY_SECOND | HOUR_MINUTE | HOUR_SECOND | MINUTE_SECOND}
```

This statement creates and schedules a new event. The event will not run unless the Event Scheduler
is enabled. For information about checking Event Scheduler status and enabling it if necessary, see
Section 23.4.2, “Event Scheduler Configuration”.

CREATE EVENT requires the EVENT privilege for the schema in which the event is to be created. It might
also require the SET_USER_ID or SUPER privilege, depending on the DEFINER value, as described later in
this section.

The minimum requirements for a valid CREATE EVENT statement are as follows:
* The keywords CREATE EVENT plus an event name, which uniquely identifies the event in a database
schema.
* An ON SCHEDULE clause, which determines when and how often the event executes.
* A DO clause, which contains the SQL statement to be executed by an event.

This is an example of a minimal CREATE EVENT statement:
```
CREATE EVENT myevent
    ON SCHEDULE AT CURRENT_TIMESTAMP + INTERVAL 1 HOUR
    DO
        UPDATE myschema.mytable SET mycol = mycol + 1;
```

The previous statement creates an event named myevent. This event executes once—one hour following
its creation—by running an SQL statement that increments the value of the myschema.mytable table's
mycol column by 1.

The event_name must be a valid MySQL identifier with a maximum length of 64 characters. Event
names are not case-sensitive, so you cannot have two events named myevent and MyEvent in the same
schema. In general, the rules governing event names are the same as those for names of stored routines.
See Section 9.2, “Schema Object Names”.

An event is associated with a schema. If no schema is indicated as part of event_name, the default
(current) schema is assumed. To create an event in a specific schema, qualify the event name with a
schema using schema_name.event_name syntax.

The DEFINER clause specifies the MySQL account to be used when checking access privileges
at event execution time. If a user value is given, it should be a MySQL account specified as
'user_name'@'host_name', CURRENT_USER, or CURRENT_USER(). The default DEFINER value
is the user who executes the CREATE EVENT statement. This is the same as specifying DEFINER =
CURRENT_USER explicitly.

If you specify the DEFINER clause, these rules determine the valid DEFINER user values:
* If you do not have the SET_USER_ID or SUPER privilege, the only permitted user value is your own
account, either specified literally or by using CURRENT_USER. You cannot set the definer to some other
account.
* If you have the SET_USER_ID or SUPER privilege, you can specify any syntactically valid account name.
If the account does not exist, a warning is generated.
* Although it is possible to create an event with a nonexistent DEFINER account, an error occurs at event
execution time if the account does not exist.

For more information about event security, see Section 23.6, “Access Control for Stored Programs and
Views”.

Within an event, the CURRENT_USER() function returns the account used to check privileges at event
execution time, which is the DEFINER user. For information about user auditing within events, see
Section 6.3.13, “SQL-Based MySQL Account Activity Auditing”.

IF NOT EXISTS has the same meaning for CREATE EVENT as for CREATE TABLE: If an event named
event_name already exists in the same schema, no action is taken, and no error results. (However, a
warning is generated in such cases.)

The ON SCHEDULE clause determines when, how often, and for how long the event_body defined for the
event repeats. This clause takes one of two forms:
* AT timestamp is used for a one-time event. It specifies that the event executes one time only
  at the date and time given by timestamp, which must include both the date and time, or must be
  an expression that resolves to a datetime value. You may use a value of either the DATETIME or
  TIMESTAMP type for this purpose. If the date is in the past, a warning occurs, as shown here:
  ```
  mysql> SELECT NOW();
  +---------------------+
  | NOW() |
  +---------------------+
  | 2006-02-10 23:59:01 |
  +---------------------+
  1 row in set (0.04 sec)
  
  mysql> CREATE EVENT e_totals
  -> ON SCHEDULE AT '2006-02-10 23:59:00'
  -> DO INSERT INTO test.totals VALUES (NOW());
  Query OK, 0 rows affected, 1 warning (0.00 sec)
  
  mysql> SHOW WARNINGS\G
  *************************** 1. row ***************************
  Level: Note
  Code: 1588
  Message: Event execution time is in the past and ON COMPLETION NOT
  PRESERVE is set. The event was dropped immediately after
  creation.
  ```
  
  CREATE EVENT statements which are themselves invalid—for whatever reason—fail with an error.
  
  You may use CURRENT_TIMESTAMP to specify the current date and time. In such a case, the event acts
  as soon as it is created.
  
  To create an event which occurs at some point in the future relative to the current date and time—such
  as that expressed by the phrase “three weeks from now”—you can use the optional clause + INTERVAL
  interval. The interval portion consists of two parts, a quantity and a unit of time, and follows the
  same syntax rules that govern intervals used in the DATE_ADD() function (see Section 12.7, “Date
  and Time Functions”. The units keywords are also the same, except that you cannot use any units
  involving microseconds when defining an event. With some interval types, complex time units may
  be used. For example, “two minutes and ten seconds” can be expressed as + INTERVAL '2:10'
  MINUTE_SECOND.
  
  You can also combine intervals. For example, AT CURRENT_TIMESTAMP + INTERVAL 3 WEEK +
  INTERVAL 2 DAY is equivalent to “three weeks and two days from now”. Each portion of such a clause
  must begin with + INTERVAL.

* To repeat actions at a regular interval, use an EVERY clause. The EVERY keyword is followed by an
  interval as described in the previous discussion of the AT keyword. (+ INTERVAL is not used with
  EVERY.) For example, EVERY 6 WEEK means “every six weeks”.
  
  Although + INTERVAL clauses are not permitted in an EVERY clause, you can use the same complex
  time units permitted in a + INTERVAL.
  
  An EVERY clause may contain an optional STARTS clause. STARTS is followed by a timestamp value
  that indicates when the action should begin repeating, and may also use + INTERVAL interval to
  specify an amount of time “from now”. For example, EVERY 3 MONTH STARTS CURRENT_TIMESTAMP
  + INTERVAL 1 WEEK means “every three months, beginning one week from now”. Similarly, you
  can express “every two weeks, beginning six hours and fifteen minutes from now” as EVERY 2 WEEK
  STARTS CURRENT_TIMESTAMP + INTERVAL '6:15' HOUR_MINUTE. Not specifying STARTS is
  the same as using STARTS CURRENT_TIMESTAMP—that is, the action specified for the event begins
  repeating immediately upon creation of the event.
  
  An EVERY clause may contain an optional ENDS clause. The ENDS keyword is followed by a timestamp
  value that tells MySQL when the event should stop repeating. You may also use + INTERVAL
  interval with ENDS; for instance, EVERY 12 HOUR STARTS CURRENT_TIMESTAMP + INTERVAL
  30 MINUTE ENDS CURRENT_TIMESTAMP + INTERVAL 4 WEEK is equivalent to “every twelve hours,
  beginning thirty minutes from now, and ending four weeks from now”. Not using ENDS means that the
  event continues executing indefinitely.

  ENDS supports the same syntax for complex time units as STARTS does.

  You may use STARTS, ENDS, both, or neither in an EVERY clause.

  If a repeating event does not terminate within its scheduling interval, the result may be multiple instances
  of the event executing simultaneously. If this is undesirable, you should institute a mechanism to prevent
  simultaneous instances. For example, you could use the GET_LOCK() function, or row or table locking.

The ON SCHEDULE clause may use expressions involving built-in MySQL functions and user variables to
obtain any of the timestamp or interval values which it contains. You may not use stored functions or
user-defined functions in such expressions, nor may you use any table references; however, you may use
SELECT FROM DUAL. This is true for both CREATE EVENT and ALTER EVENT statements. References
to stored functions, user-defined functions, and tables in such cases are specifically not permitted, and fail
with an error (see Bug #22830).

Times in the ON SCHEDULE clause are interpreted using the current session time_zone value. This
becomes the event time zone; that is, the time zone that is used for event scheduling and is in effect
within the event as it executes. These times are converted to UTC and stored along with the event time
zone in the mysql.event table. This enables event execution to proceed as defined regardless of any
subsequent changes to the server time zone or daylight saving time effects. For additional information
about representation of event times, see Section 23.4.4, “Event Metadata”. See also Section 13.7.6.18,
“SHOW EVENTS Syntax”, and Section 24.9, “The INFORMATION_SCHEMA EVENTS Table”.

Normally, once an event has expired, it is immediately dropped. You can override this behavior by
specifying ON COMPLETION PRESERVE. Using ON COMPLETION NOT PRESERVE merely makes the
default nonpersistent behavior explicit.

You can create an event but prevent it from being active using the DISABLE keyword. Alternatively, you
can use ENABLE to make explicit the default status, which is active. This is most useful in conjunction with
ALTER EVENT (see Section 13.1.3, “ALTER EVENT Syntax”).

A third value may also appear in place of ENABLE or DISABLE; DISABLE ON SLAVE is set for the status
of an event on a replication slave to indicate that the event was created on the master and replicated to the
slave, but is not executed on the slave. See Section 17.4.1.16, “Replication of Invoked Features”.

You may supply a comment for an event using a COMMENT clause. comment may be any string of up to 64
characters that you wish to use for describing the event. The comment text, being a string literal, must be
surrounded by quotation marks.

The DO clause specifies an action carried by the event, and consists of an SQL statement. Nearly any
valid MySQL statement that can be used in a stored routine can also be used as the action statement
for a scheduled event. (See Section C.1, “Restrictions on Stored Programs”.) For example, the following
event e_hourly deletes all rows from the sessions table once per hour, where this table is part of the
site_activity schema:
```
CREATE EVENT e_hourly
    ON SCHEDULE
        EVERY 1 HOUR
    COMMENT 'Clears out sessions table each hour.'
    DO
        DELETE FROM site_activity.sessions;
```

MySQL stores the sql_mode system variable setting in effect when an event is created or altered, and
always executes the event with this setting in force, regardless of the current server SQL mode when the
event begins executing.

A CREATE EVENT statement that contains an ALTER EVENT statement in its DO clause appears to
succeed; however, when the server attempts to execute the resulting scheduled event, the execution fails
with an error.

    Note
    Statements such as SELECT or SHOW that merely return a result set have no effect
    when used in an event; the output from these is not sent to the MySQL Monitor,
    nor is it stored anywhere. However, you can use statements such as SELECT ...
    INTO and INSERT INTO ... SELECT that store a result. (See the next example
    in this section for an instance of the latter.)

The schema to which an event belongs is the default schema for table references in the DO clause. Any
references to tables in other schemas must be qualified with the proper schema name.

As with stored routines, you can use compound-statement syntax in the DO clause by using the BEGIN and
END keywords, as shown here:
```
delimiter |
CREATE EVENT e_daily
ON SCHEDULE
EVERY 1 DAY
COMMENT 'Saves total number of sessions then clears the table each day'
DO
BEGIN
INSERT INTO site_activity.totals (time, total)
SELECT CURRENT_TIMESTAMP, COUNT(*)
FROM site_activity.sessions;
DELETE FROM site_activity.sessions;
END |
delimiter ;
```

This example uses the delimiter command to change the statement delimiter. See Section 23.1,
“Defining Stored Programs”.

More complex compound statements, such as those used in stored routines, are possible in an event. This
example uses local variables, an error handler, and a flow control construct:
```
delimiter |

CREATE EVENT e
    ON SCHEDULE
        EVERY 5 SECOND
    DO
        BEGIN
            DECLARE v INTEGER;
            DECLARE CONTINUE HANDLER FOR SQLEXCEPTION BEGIN END;

            SET v = 0;

            WHILE v < 5 DO
                INSERT INTO t1 VALUES (0);
                UPDATE t2 SET s1 = s1 + 1;
                SET v = v + 1;
            END WHILE;
    END |

delimiter ;
```

There is no way to pass parameters directly to or from events; however, it is possible to invoke a stored
routine with parameters within an event:
```
CREATE EVENT e_call_myproc
    ON SCHEDULE
      AT CURRENT_TIMESTAMP + INTERVAL 1 DAY
    DO CALL myproc(5, 27);
```
If an event's definer has the SYSTEM_VARIABLES_ADMIN or SUPER privilege, the event can read and
write global variables. As granting this privilege entails a potential for abuse, extreme care must be taken in
doing so.

Generally, any statements that are valid in stored routines may be used for action statements executed
by events. For more information about statements permissible within stored routines, see Section 23.2.1,
“Stored Routine Syntax”. You can create an event as part of a stored routine, but an event cannot be
created by another event.

## 13.1.13 CREATE FUNCTION Syntax

The CREATE FUNCTION statement is used to create stored functions and user-defined functions (UDFs):

* For information about creating stored functions, see Section 13.1.15, “CREATE PROCEDURE and
CREATE FUNCTION Syntax”.

* For information about creating user-defined functions, see Section 13.7.4.1, “CREATE FUNCTION
Syntax for User-Defined Functions”.

## 13.1.14 CREATE INDEX Syntax

```
CREATE [UNIQUE | FULLTEXT | SPATIAL] INDEX index_name
    [index_type]
    ON tbl_name (key_part,...)
    [index_option]
    [algorithm_option | lock_option] ...

key_part: {col_name [(length)] | (expr)} [ASC | DESC]

index_option:
    KEY_BLOCK_SIZE [=] value
    | index_type
    | WITH PARSER parser_name
    | COMMENT 'string'
    | {VISIBLE | INVISIBLE}

index_type:
    USING {BTREE | HASH}

algorithm_option:
    ALGORITHM [=] {DEFAULT | INPLACE | COPY}

lock_option:
    LOCK [=] {DEFAULT | NONE | SHARED | EXCLUSIVE}
```

Normally, you create all indexes on a table at the time the table itself is created with CREATE TABLE. See
Section 13.1.18, “CREATE TABLE Syntax”. This guideline is especially important for InnoDB tables, where
the primary key determines the physical layout of rows in the data file. CREATE INDEX enables you to add
indexes to existing tables.

CREATE INDEX is mapped to an ALTER TABLE statement to create indexes. See Section 13.1.8, “ALTER
TABLE Syntax”. CREATE INDEX cannot be used to create a PRIMARY KEY; use ALTER TABLE instead.
For more information about indexes, see Section 8.3.1, “How MySQL Uses Indexes”.

InnoDB supports secondary indexes on virtual columns. For more information, see Section 13.1.18.9,
“Secondary Indexes and Generated Columns”.

When the innodb_stats_persistent setting is enabled, run the ANALYZE TABLE statement for an
InnoDB table after creating an index on that table.

An index specification of the form (key_part1, key_part2, ...) creates an index with multiple
key parts. Index key values are formed by concatenating the values of the given key parts. For example
(col1, col2, col3) specifies a multiple-column index with index keys consisting of values from col1,
col2, and col3.

A key_part specification can end with ASC or DESC to specify whether index values are stored in
ascending or descending order. The default is ascending if no order specifier is given. ASC and DESC
are not permitted for HASH indexes. As of MySQL 8.0.12, ASC and DESC are not permitted for SPATIAL
indexes.

The following sections describe different aspects of the CREATE INDEX statement:
* Column Prefix Key Parts
* Functional Key Parts
* Unique Indexes
* Full-Text Indexes
* Spatial Indexes
* Index Options
* Table Copying and Locking Options

### Column Prefix Key Parts

For string columns, indexes can be created that use only the leading part of column values, using
col_name(length) syntax to specify an index prefix length:
• Prefixes can be specified for CHAR, VARCHAR, BINARY, and VARBINARY key parts.
• Prefixes must be specified for BLOB and TEXT key parts. Additionally, BLOB and TEXT columns can be
indexed only for InnoDB, MyISAM, and BLACKHOLE tables.
• Prefix limits are measured in bytes. However, prefix lengths for index specifications in CREATE TABLE,
ALTER TABLE, and CREATE INDEX statements are interpreted as number of characters for nonbinary
string types (CHAR, VARCHAR, TEXT) and number of bytes for binary string types (BINARY, VARBINARY,
BLOB). Take this into account when specifying a prefix length for a nonbinary string column that uses a
multibyte character set.

Prefix support and lengths of prefixes (where supported) are storage engine dependent. For example, a
prefix can be up to 767 bytes long for InnoDB tables that use the REDUNDANT or COMPACT row format.
The prefix length limit is 3072 bytes for InnoDB tables that use the DYNAMIC or COMPRESSED row
format. For MyISAM tables, the prefix length limit is 1000 bytes.

If a specified index prefix exceeds the maximum column data type size, CREATE INDEX handles the index
as follows:
• For a nonunique index, either an error occurs (if strict SQL mode is enabled), or the index length is
reduced to lie within the maximum column data type size and a warning is produced (if strict SQL mode
is not enabled).
• For a unique index, an error occurs regardless of SQL mode because reducing the index length might
enable insertion of nonunique entries that do not meet the specified uniqueness requirement.

The statement shown here creates an index using the first 10 characters of the name column (assuming
that name has a nonbinary string type):
```
CREATE INDEX part_of_name ON customer (name(10));
```
If names in the column usually differ in the first 10 characters, lookups performed using this index should
not be much slower than using an index created from the entire name column. Also, using column prefixes
for indexes can make the index file much smaller, which could save a lot of disk space and might also
speed up INSERT operations.

### Functional Key Parts

A “normal” index indexes column values or prefixes of column values. For example, in the following table,
the index entry for a given t1 row includes the full col1 value and a prefix of the col2 value consisting of
its first 10 bytes:
```
CREATE TABLE t1 (
col1 VARCHAR(10),
col2 VARCHAR(20),
INDEX (col1, col2(10))
);
```

MySQL 8.0.13 and higher supports functional key parts that index expression values rather than column or
column prefix values. Use of functional key parts enables indexing of values not stored directly in the table.
Examples:
```
CREATE TABLE t1 (col1 INT, col2 INT, INDEX func_index ((ABS(col1))));
CREATE INDEX idx1 ON t1 ((col1 + col2));
CREATE INDEX idx2 ON t1 ((col1 + col2), (col1 - col2), col1);
ALTER TABLE t1 ADD INDEX ((col1 * 40) DESC);
```
An index with multiple key parts can mix nonfunctional and functional key parts.

ASC and DESC are supported for functional key parts.

Functional key parts must adhere to the following rules. An error occurs if a key part definition contains
disallowed constructs.

* In index definitions, enclose expressions within parentheses to distinguish them from columns or column
prefixes. For example, this is permitted; the expressions are enclosed within parentheses:

* In index definitions, enclose expressions within parentheses to distinguish them from columns or column
prefixes. For example, this is permitted; the expressions are enclosed within parentheses:
```
INDEX ((col1 + col2), (col3 - col4))
```
This produces an error; the expressions are not enclosed within parentheses:
```
INDEX (col1 + col2, col3 - col4)
```
* A functional key part cannot consist solely of a column name. For example, this is not permitted:
```
INDEX ((col1), (col2))
```
Instead, write the key parts as nonfunctional key parts, without parentheses:
```
INDEX (col1, col2)
```
* A functional key part expression cannot refer to column prefixes. For a workaround, see the discussion
of SUBSTRING() and CAST() later in this section.
* Functional key parts are not permitted in foreign key specifications.

For CREATE TABLE ... LIKE, the destination table preserves functional key parts from the original
table.

Functional indexes are implemented as hidden virtual generated columns, which has these implications:
• Each functional key part counts against the limit on total number of table columns; see Section C.10.4,
“Limits on Table Column Count and Row Size”.
• Functional key parts inherit all restrictions that apply to generated columns. Examples:
• Only functions permitted for generated columns are permitted for functional key parts.
• Subqueries, parameters, variables, stored functions, and user-defined functions are not permitted.

For more information about applicable restrictions, see Section 13.1.18.8, “CREATE TABLE and
Generated Columns”, and Section 13.1.8.2, “ALTER TABLE and Generated Columns”.

UNIQUE is supported for indexes that include functional key parts. However, primary keys cannot include
functional key parts. A primary key requires the generated column to be stored, but functional key parts are
implemented as virtual generated columns, not stored generated columns.

SPATIAL and FULLTEXT indexes cannot have functional key parts.
If a table contains no primary key, InnoDB automatically promotes the first UNIQUE NOT NULL index to
the primary key. This is not supported for UNIQUE NOT NULL indexes that have functional key parts.
Nonfunctional indexes raise a warning if there are duplicate indexes. Indexes that contain functional key
parts do not have this feature.

To remove a column that is referenced by a functional key part, the index must be removed first.
Otherwise, an error occurs.

Although nonfunctional key parts support a prefix length specification, this is not possible for functional key
parts. The solution is to use SUBSTRING() (or CAST(), as described later in this section). For a functional
key part containing the SUBSTRING() function to be used in a query, the WHERE clause must contain
SUBSTRING() with the same arguments. In the following example, only the second SELECT is able to
use the index because that is the only query in which the arguments to SUBSTRING() match the index
specification:
```
CREATE TABLE tbl (
col1 LONGTEXT,
INDEX idx1 ((SUBSTRING(col1, 1, 10)))
);
SELECT * FROM tbl WHERE SUBSTRING(col1, 1, 9) = '123456789';
SELECT * FROM tbl WHERE SUBSTRING(col1, 1, 10) = '1234567890';
```

Functional key parts enable indexing of values that cannot be indexed otherwise, such as JSON values.
However, this must be done correctly to achieve the desired effect. For example, this syntax does not
work:
```
CREATE TABLE employees (
    data JSON,
    INDEX ((data->>'$.name'))
);
```

The syntax fails because:
• The ->> operator translates into JSON_UNQUOTE(JSON_EXTRACT(...)).
• JSON_UNQUOTE() returns a value with a data type of LONGTEXT, and the hidden generated column thus
is assigned the same data type.
• MySQL cannot index LONGTEXT columns specified without a prefix length on the key part, and prefix
lengths are not permitted in functional key parts.

To index the JSON column, you could try using the CAST() function as follows:
```
CREATE TABLE employees (
data JSON,
INDEX ((CAST(data->>'$.name' AS CHAR(30))))
);
```

The hidden generated column is assigned the VARCHAR(30) data type, which can be indexed. But this
approach produces a new issue when trying to use the index:
• CAST() returns a string with the collation utf8mb4_0900_ai_ci (the server default collation).
• JSON_UNQUOTE() returns a string with the collation utf8mb4_bin (hard coded).
As a result, there is a collation mismatch between the indexed expression in the preceding table definition
and the WHERE clause expression in the following query:
```
SELECT * FROM employees WHERE data->>'$.name' = 'James';
```

The index is not used because the expressions in the query and the index differ. To support this kind of
scenario for functional key parts, the optimizer automatically strips CAST() when looking for an index
to use, but only if the collation of the indexed expression matches that of the query expression. For an
index with a functional key part to be used, either of the following two solutions work (although they differ
somewhat in effect):
• Solution 1. Assign the indexed expression the same collation as JSON_UNQUOTE():
```
CREATE TABLE employees (
data JSON,
INDEX idx ((CAST(data->>"$.name" AS CHAR(30)) COLLATE utf8mb4_bin))
);
INSERT INTO employees VALUES
('{ "name": "james", "salary": 9000 }'),
('{ "name": "James", "salary": 10000 }'),
('{ "name": "Mary", "salary": 12000 }'),
('{ "name": "Peter", "salary": 8000 }');
SELECT * FROM employees WHERE data->>'$.name' = 'James';
```

The `->>` operator is the same as `JSON_UNQUOTE(JSON_EXTRACT(...))`, and `JSON_UNQUOTE()`
returns a string with collation `utf8mb4_bin`. The comparison is thus case sensitive, and only one row
matches:
```
+------------------------------------+
| data |
+------------------------------------+
| {"name": "James", "salary": 10000} |
+------------------------------------+
```

* Solution 2. Specify the full expression in the query:
```
CREATE TABLE employees (
    data JSON,
    INDEX idx ((CAST(data->>"$.name" AS CHAR(30))))
);

INSERT INTO employees VALUES
('{ "name": "james", "salary": 9000 }'),
('{ "name": "James", "salary": 10000 }'),
('{ "name": "Mary", "salary": 12000 }'),
('{ "name": "Peter", "salary": 8000 }');

SELECT * FROM employees WHERE CAST(data->>'$.name' AS CHAR(30)) = 'James';
```

CAST() returns a string with collation utf8mb4_0900_ai_ci, so the comparison case insensitive and
two rows match:
```
+------------------------------------+
| data |
+------------------------------------+
| {"name": "james", "salary": 9000} |
| {"name": "James", "salary": 10000} |
+------------------------------------+
```

Be aware that although the the optimizer supports automatically stripping CAST() with indexed generated
columns, the following approach does not work because it produces a different result with and without an
index (Bug#27337092):
```
mysql> CREATE TABLE employees (
    data JSON,
    generated_col VARCHAR(30) AS (CAST(data->>'$.name' AS CHAR(30)))
);
Query OK, 0 rows affected, 1 warning (0.03 sec)

mysql> INSERT INTO employees (data)
VALUES ('{"name": "james"}'), ('{"name": "James"}');
Query OK, 2 rows affected, 1 warning (0.01 sec)
Records: 2 Duplicates: 0 Warnings: 1

mysql> SELECT * FROM employees WHERE data->>'$.name' = 'James';
+-------------------+---------------+
| data | generated_col |
+-------------------+---------------+
| {"name": "James"} | James |
+-------------------+---------------+
1 row in set (0.00 sec)

mysql> ALTER TABLE employees ADD INDEX idx (generated_col);
Query OK, 0 rows affected, 1 warning (0.03 sec)
Records: 0 Duplicates: 0 Warnings: 1

mysql> SELECT * FROM employees WHERE data->>'$.name' = 'James';
+-------------------+---------------+
| data | generated_col |
+-------------------+---------------+
| {"name": "james"} | james |
| {"name": "James"} | James |
+-------------------+---------------+
2 rows in set (0.01 sec)
```

### Unique Indexes

A UNIQUE index creates a constraint such that all values in the index must be distinct. An error occurs if
you try to add a new row with a key value that matches an existing row. If you specify a prefix value for a
column in a UNIQUE index, the column values must be unique within the prefix length. A UNIQUE index
permits multiple NULL values for columns that can contain NULL.

If a table has a PRIMARY KEY or UNIQUE NOT NULL index that consists of a single column that has an
integer type, you can use `_rowid` to refer to the indexed column in SELECT statements, as follows:
* `_rowid` refers to the PRIMARY KEY column if there is a PRIMARY KEY consisting of a single integer
column. If there is a PRIMARY KEY but it does not consist of a single integer column, `_rowid` cannot be
used.
* Otherwise, `_rowid` refers to the column in the first UNIQUE NOT NULL index if that index consists of a
single integer column. If the first UNIQUE NOT NULL index does not consist of a single integer column,
`_rowid` cannot be used.

### Full-Text Indexes

FULLTEXT indexes are supported only for InnoDB and MyISAM tables and can include only CHAR,
VARCHAR, and TEXT columns. Indexing always happens over the entire column; column prefix indexing is
not supported and any prefix length is ignored if specified. See Section 12.9, “Full-Text Search Functions”,
for details of operation.

### Spatial Indexes

The MyISAM, InnoDB, NDB, and ARCHIVE storage engines support spatial columns such as POINT and
GEOMETRY. (Section 11.5, “Spatial Data Types”, describes the spatial data types.) However, support for
spatial column indexing varies among engines. Spatial and nonspatial indexes on spatial columns are
available according to the following rules.

Spatial indexes on spatial columns have these characteristics:
• Available only for InnoDB and MyISAM tables. Specifying SPATIAL INDEX for other storage engines
results in an error.
• As of MySQL 8.0.12, an index on a spatial column must be a SPATIAL index. The SPATIAL keyword is
thus optional but implicit for creating an index on a spatial column.
• Available for single spatial columns only. A spatial index cannot be created over multiple spatial
columns.
• Indexed columns must be NOT NULL.
• Column prefix lengths are prohibited. The full width of each column is indexed.
• Not permitted for a primary key or unique index.

Nonspatial indexes on spatial columns (created with INDEX, UNIQUE, or PRIMARY KEY) have these
characteristics:
• Permitted for any storage engine that supports spatial columns except ARCHIVE.
• Columns can be NULL unless the index is a primary key.
• The index type for a non-SPATIAL index depends on the storage engine. Currently, B-tree is used.
• Permitted for a column that can have NULL values only for InnoDB, MyISAM, and MEMORY tables.

### Index Options

Following the key part list, index options can be given. An index_option value can be any of the
following:

* KEY_BLOCK_SIZE [=] value

  For MyISAM tables, KEY_BLOCK_SIZE optionally specifies the size in bytes to use for index key blocks.
  The value is treated as a hint; a different size could be used if necessary. A KEY_BLOCK_SIZE value
  specified for an individual index definition overrides a table-level KEY_BLOCK_SIZE value.
  
  KEY_BLOCK_SIZE is not supported at the index level for InnoDB tables. See Section 13.1.18, “CREATE
  TABLE Syntax”.

* index_type

  Some storage engines permit you to specify an index type when creating an index. For example:

  ```
  CREATE TABLE lookup (id INT) ENGINE = MEMORY;
  CREATE INDEX id_index ON lookup (id) USING BTREE;
  ```

  Table 13.1, “Index Types Per Storage Engine” shows the permissible index type values supported by
  different storage engines. Where multiple index types are listed, the first one is the default when no index
  type specifier is given. Storage engines not listed in the table do not support an index_type clause in
  index definitions.

    Table 13.1 Index Types Per Storage Engine
    -------------------------------------------
    Storage Engine          Permissible Index Types
    InnoDB                  BTREE
    MyISAM                  BTREE
    MEMORY/HEAP             HASH, BTREE

The index_type clause cannot be used for FULLTEXT INDEX or (prior to MySQL 8.0.12) SPATIAL
INDEX specifications. Full-text index implementation is storage engine dependent. Spatial indexes are
implemented as R-tree indexes.

If you specify an index type that is not valid for a given storage engine, but another index type is
available that the engine can use without affecting query results, the engine uses the available type.
The parser recognizes RTREE as a type name. As of MySQL 8.0.12, this is permitted only for SPATIAL
indexes. Prior to 8.0.12, RTREE cannot be specified for any storage engine.

    Note
    Use of the index_type option before the ON tbl_name clause is deprecated;
    support for use of the option in this position will be removed in a future MySQL
    release. If an index_type option is given in both the earlier and later positions,
    the final option applies.

TYPE type_name is recognized as a synonym for USING type_name. However, USING is the
preferred form.

The following tables show index characteristics for the storage engines that support the index_type
option.

    Table 13.2 InnoDB Storage Engine Index Characteristics
    Index Class Index
    Type
    Stores NULL
    VALUES
    Permits Multiple
    NULL Values
    IS NULL Scan
    Type
    IS NOT NULL
    Scan Type
    Primary key BTREE No No N/A N/A
    Unique BTREE Yes Yes Index Index
    Key BTREE Yes Yes Index Index
    FULLTEXT N/A Yes Yes Table Table
    SPATIAL N/A No No N/A N/A

    Table 13.3 MyISAM Storage Engine Index Characteristics
    Index Class Index
    Type
    Stores NULL
    VALUES
    Permits Multiple
    NULL Values
    IS NULL Scan
    Type
    IS NOT NULL
    Scan Type
    Primary key BTREE No No N/A N/A
    Unique BTREE Yes Yes Index Index
    Key BTREE Yes Yes Index Index
    FULLTEXT N/A Yes Yes Table Table
    SPATIAL N/A No No N/A N/A

    Table 13.4 MEMORY Storage Engine Index Characteristics
    Index Class Index
    Type
    Stores NULL
    VALUES
    Permits Multiple
    NULL Values
    IS NULL Scan
    Type
    IS NOT NULL
    Scan Type
    Primary key BTREE No No N/A N/A
    Unique BTREE Yes Yes Index Index
    Key BTREE Yes Yes Index Index
    Primary key HASH No No N/A N/A
    Unique HASH Yes Yes Index Index
    Key HASH Yes Yes Index Index

• WITH PARSER parser_name

This option can be used only with FULLTEXT indexes. It associates a parser plugin with the index if full-
text indexing and searching operations need special handling. InnoDB and MyISAM support full-text
parser plugins. See Full-Text Parser Plugins and Section 28.2.4.4, “Writing Full-Text Parser Plugins” for
more information.

• COMMENT 'string'

  Index definitions can include an optional comment of up to 1024 characters.
  
  The MERGE_THRESHOLD for index pages can be configured for individual indexes using the
  index_option COMMENT clause of the CREATE INDEX statement. For example:
  ```
  CREATE TABLE t1 (id INT);
  CREATE INDEX id_index ON t1 (id) COMMENT 'MERGE_THRESHOLD=40';
  ```
  
  If the page-full percentage for an index page falls below the MERGE_THRESHOLD value when a row is
  deleted or when a row is shortened by an update operation, InnoDB attempts to merge the index page
  with a neighboring index page. The default MERGE_THRESHOLD value is 50, which is the previously
  hardcoded value.
  
  MERGE_THRESHOLD can also be defined at the index level and table level using CREATE TABLE
  and ALTER TABLE statements. For more information, see Section 15.6.12, “Configuring the Merge
  Threshold for Index Pages”.

• VISIBLE, INVISIBLE

  Specify index visibility. Indexes are visible by default. An invisible index is not used by the optimizer.
  Specification of index visibility applies to indexes other than primary keys (either explicit or implicit). For
  more information, see Section 8.3.12, “Invisible Indexes”.

### Table Copying and Locking Options

ALGORITHM and LOCK clauses may be given to influence the table copying method and level of
concurrency for reading and writing the table while its indexes are being modified. They have the same
meaning as for the ALTER TABLE statement. For more information, see Section 13.1.8, “ALTER TABLE
Syntax”

## 13.1.15 CREATE PROCEDURE and CREATE FUNCTION Syntax

```
CREATE
    [DEFINER = { user | CURRENT_USER }]
    PROCEDURE sp_name ([proc_parameter[,...]])
    [characteristic ...] routine_body

CREATE
    [DEFINER = { user | CURRENT_USER }]
    FUNCTION sp_name ([func_parameter[,...]])
    RETURNS type
    [characteristic ...] routine_body

proc_parameter:
    [ IN | OUT | INOUT ] param_name type

func_parameter:
    param_name type

type:
    Any valid MySQL data type

characteristic:
    COMMENT 'string'
    | LANGUAGE SQL
    | [NOT] DETERMINISTIC
    | { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
    | SQL SECURITY { DEFINER | INVOKER }

routine_body:
    Valid SQL routine statement
```

The CREATE FUNCTION statement is also used in MySQL to support UDFs (user-defined functions). See
Section 28.4, “Adding New Functions to MySQL”. A UDF can be regarded as an external stored function.

Stored functions share their namespace with UDFs. See Section 9.2.4, “Function Name Parsing and
Resolution”, for the rules describing how the server interprets references to different kinds of functions.

To invoke a stored procedure, use the CALL statement (see Section 13.2.1, “CALL Syntax”). To invoke a
stored function, refer to it in an expression. The function returns a value during expression evaluation.

CREATE PROCEDURE and CREATE FUNCTION require the CREATE ROUTINE privilege. They might also
require the SET_USER_ID or SUPER privilege, depending on the DEFINER value, as described later in this
section. If binary logging is enabled, CREATE FUNCTION might require the SUPER privilege, as described
in Section 23.7, “Binary Logging of Stored Programs”.

By default, MySQL automatically grants the ALTER ROUTINE and EXECUTE privileges to the routine
creator. This behavior can be changed by disabling the automatic_sp_privileges system variable.
See Section 23.2.2, “Stored Routines and MySQL Privileges”.

The DEFINER and SQL SECURITY clauses specify the security context to be used when checking access
privileges at routine execution time, as described later in this section.

If the routine name is the same as the name of a built-in SQL function, a syntax error occurs unless you
use a space between the name and the following parenthesis when defining the routine or invoking it later.

For this reason, avoid using the names of existing SQL functions for your own stored routines.

The IGNORE_SPACE SQL mode applies to built-in functions, not to stored routines. It is always permissible
to have spaces after a stored routine name, regardless of whether IGNORE_SPACE is enabled.

The parameter list enclosed within parentheses must always be present. If there are no parameters, an
empty parameter list of () should be used. Parameter names are not case sensitive.

Each parameter is an IN parameter by default. To specify otherwise for a parameter, use the keyword OUT
or INOUT before the parameter name.

    Note
    Specifying a parameter as IN, OUT, or INOUT is valid only for a PROCEDURE. For a
    FUNCTION, parameters are always regarded as IN parameters.

An IN parameter passes a value into a procedure. The procedure might modify the value, but the
modification is not visible to the caller when the procedure returns. An OUT parameter passes a value from
the procedure back to the caller. Its initial value is NULL within the procedure, and its value is visible to the
caller when the procedure returns. An INOUT parameter is initialized by the caller, can be modified by the
procedure, and any change made by the procedure is visible to the caller when the procedure returns.

For each OUT or INOUT parameter, pass a user-defined variable in the CALL statement that invokes the
procedure so that you can obtain its value when the procedure returns. If you are calling the procedure
from within another stored procedure or function, you can also pass a routine parameter or local routine
variable as an OUT or INOUT parameter. If you are calling the procedure from within a trigger, you can also
pass NEW.col_name as an OUT or INOUT parameter.

Routine parameters cannot be referenced in statements prepared within the routine; see Section C.1,
“Restrictions on Stored Programs”.

The following example shows a simple stored procedure that uses an OUT parameter:
```
mysql> delimiter //

mysql> CREATE PROCEDURE simpleproc (OUT param1 INT)
-> BEGIN
-> SELECT COUNT(*) INTO param1 FROM t;
-> END//
Query OK, 0 rows affected (0.00 sec)

mysql> delimiter ;

mysql> CALL simpleproc(@a);
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @a;
+------+
| @a |
+------+
| 3 |
+------+
1 row in set (0.00 sec)
```

The example uses the mysql client delimiter command to change the statement delimiter from ; to //
while the procedure is being defined. This enables the ; delimiter used in the procedure body to be passed
through to the server rather than being interpreted by mysql itself. See Section 23.1, “Defining Stored
Programs”.

The RETURNS clause may be specified only for a FUNCTION, for which it is mandatory. It indicates the
return type of the function, and the function body must contain a RETURN value statement. If the RETURN
statement returns a value of a different type, the value is coerced to the proper type. For example, if a
function specifies an ENUM or SET value in the RETURNS clause, but the RETURN statement returns an
integer, the value returned from the function is the string for the corresponding ENUM member of set of SET
members.

The following example function takes a parameter, performs an operation using an SQL function, and
returns the result. In this case, it is unnecessary to use delimiter because the function definition
contains no internal ; statement delimiters:
```
mysql> CREATE FUNCTION hello (s CHAR(20))

mysql> RETURNS CHAR(50) DETERMINISTIC
-> RETURN CONCAT('Hello, ',s,'!');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT hello('world');
+----------------+
| hello('world') |
+----------------+
| Hello, world! |
+----------------+
1 row in set (0.00 sec)
```

Parameter types and function return types can be declared to use any valid data type. The COLLATE
attribute can be used if preceded by the CHARACTER SET attribute.

The routine_body consists of a valid SQL routine statement. This can be a simple statement such as
SELECT or INSERT, or a compound statement written using BEGIN and END. Compound statements can
contain declarations, loops, and other control structure statements. The syntax for these statements is
described in Section 13.6, “Compound-Statement Syntax”.

MySQL permits routines to contain DDL statements, such as CREATE and DROP. MySQL also permits
stored procedures (but not stored functions) to contain SQL transaction statements such as COMMIT.

Stored functions may not contain statements that perform explicit or implicit commit or rollback. Support for
these statements is not required by the SQL standard, which states that each DBMS vendor may decide
whether to permit them.

Statements that return a result set can be used within a stored procedure but not within a stored function.
This prohibition includes SELECT statements that do not have an INTO var_list clause and other
statements such as SHOW, EXPLAIN, and CHECK TABLE. For statements that can be determined at
function definition time to return a result set, a Not allowed to return a result set from a
function error occurs (ER_SP_NO_RETSET). For statements that can be determined only at runtime to
return a result set, a PROCEDURE %s can't return a result set in the given context error
occurs (ER_SP_BADSELECT).

USE statements within stored routines are not permitted. When a routine is invoked, an implicit USE
db_name is performed (and undone when the routine terminates). The causes the routine to have the
given default database while it executes. References to objects in databases other than the routine default
database should be qualified with the appropriate database name.

For additional information about statements that are not permitted in stored routines, see Section C.1,
“Restrictions on Stored Programs”.

For information about invoking stored procedures from within programs written in a language that has a
MySQL interface, see Section 13.2.1, “CALL Syntax”.

MySQL stores the sql_mode system variable setting in effect when a routine is created or altered, and
always executes the routine with this setting in force, regardless of the current server SQL mode when the
routine begins executing.

The switch from the SQL mode of the invoker to that of the routine occurs after evaluation of arguments
and assignment of the resulting values to routine parameters. If you define a routine in strict SQL mode
but invoke it in nonstrict mode, assignment of arguments to routine parameters does not take place in strict
mode. If you require that expressions passed to a routine be assigned in strict SQL mode, you should
invoke the routine with strict mode in effect.

The COMMENT characteristic is a MySQL extension, and may be used to describe the stored routine. This
information is displayed by the SHOW CREATE PROCEDURE and SHOW CREATE FUNCTION statements.
The LANGUAGE characteristic indicates the language in which the routine is written. The server ignores this
characteristic; only SQL routines are supported.

A routine is considered “deterministic” if it always produces the same result for the same input parameters,
and “not deterministic” otherwise. If neither DETERMINISTIC nor NOT DETERMINISTIC is given in the
routine definition, the default is NOT DETERMINISTIC. To declare that a function is deterministic, you must
specify DETERMINISTIC explicitly.

Assessment of the nature of a routine is based on the “honesty” of the creator: MySQL does not check that
a routine declared DETERMINISTIC is free of statements that produce nondeterministic results. However,
misdeclaring a routine might affect results or affect performance. Declaring a nondeterministic routine as
DETERMINISTIC might lead to unexpected results by causing the optimizer to make incorrect execution
plan choices. Declaring a deterministic routine as NONDETERMINISTIC might diminish performance by
causing available optimizations not to be used.

If binary logging is enabled, the DETERMINISTIC characteristic affects which routine definitions MySQL
accepts. See Section 23.7, “Binary Logging of Stored Programs”.

A routine that contains the NOW() function (or its synonyms) or RAND() is nondeterministic, but it might
still be replication-safe. For NOW(), the binary log includes the timestamp and replicates correctly. RAND()
also replicates correctly as long as it is called only a single time during the execution of a routine. (You can
consider the routine execution timestamp and random number seed as implicit inputs that are identical on
the master and slave.)

Several characteristics provide information about the nature of data use by the routine. In MySQL, these
characteristics are advisory only. The server does not use them to constrain what kinds of statements a
routine will be permitted to execute.

* CONTAINS SQL indicates that the routine does not contain statements that read or write data. This is the
default if none of these characteristics is given explicitly. Examples of such statements are SET @x = 1
or DO RELEASE_LOCK('abc'), which execute but neither read nor write data.
* NO SQL indicates that the routine contains no SQL statements.
* READS SQL DATA indicates that the routine contains statements that read data (for example, SELECT),
but not statements that write data.
* MODIFIES SQL DATA indicates that the routine contains statements that may write data (for example,
INSERT or DELETE).

The SQL SECURITY characteristic can be DEFINER or INVOKER to specify the security context; that is,
whether the routine executes using the privileges of the account named in the routine DEFINER clause or
the user who invokes it. This account must have permission to access the database with which the routine
is associated. The default value is DEFINER. The user who invokes the routine must have the EXECUTE
privilege for it, as must the DEFINER account if the routine executes in definer security context.
The DEFINER clause specifies the MySQL account to be used when checking access privileges at routine
execution time for routines that have the SQL SECURITY DEFINER characteristic.

If a user value is given for the DEFINER clause, it should be a MySQL account specified as
'user_name'@'host_name', CURRENT_USER, or CURRENT_USER(). The default DEFINER value is
the user who executes the CREATE PROCEDURE or CREATE FUNCTION statement. This is the same as
specifying DEFINER = CURRENT_USER explicitly.

If you specify the DEFINER clause, these rules determine the valid DEFINER user values:
* If you do not have the SET_USER_ID or SUPER privilege, the only permitted user value is your own
account, either specified literally or by using CURRENT_USER. You cannot set the definer to some other
account.
* If you have the SET_USER_ID or SUPER privilege, you can specify any syntactically valid account name.
If the account does not exist, a warning is generated.
* Although it is possible to create a routine with a nonexistent DEFINER account, an error occurs at routine
execution time if the SQL SECURITY value is DEFINER but the definer account does not exist.

For more information about stored routine security, see Section 23.6, “Access Control for Stored Programs
and Views”.

Within a stored routine that is defined with the SQL SECURITY DEFINER characteristic, CURRENT_USER
returns the routine's DEFINER value. For information about user auditing within stored routines, see
Section 6.3.13, “SQL-Based MySQL Account Activity Auditing”.

Consider the following procedure, which displays a count of the number of MySQL accounts listed in the
mysql.user table:
```
CREATE DEFINER = 'admin'@'localhost' PROCEDURE account_count()
BEGIN
SELECT 'Number of accounts:', COUNT(*) FROM mysql.user;
END;
```
The procedure is assigned a DEFINER account of 'admin'@'localhost' no matter which user defines
it. It executes with the privileges of that account no matter which user invokes it (because the default
security characteristic is DEFINER). The procedure succeeds or fails depending on whether invoker has
the EXECUTE privilege for it and 'admin'@'localhost' has the SELECT privilege for the mysql.user
table.

Now suppose that the procedure is defined with the SQL SECURITY INVOKER characteristic:
```
CREATE DEFINER = 'admin'@'localhost' PROCEDURE account_count()
SQL SECURITY INVOKER
BEGIN
SELECT 'Number of accounts:', COUNT(*) FROM mysql.user;
END;
```
The procedure still has a DEFINER of 'admin'@'localhost', but in this case, it executes with the
privileges of the invoking user. Thus, the procedure succeeds or fails depending on whether the invoker
has the EXECUTE privilege for it and the SELECT privilege for the mysql.user table.

The server handles the data type of a routine parameter, local routine variable created with DECLARE, or
function return value as follows:
* Assignments are checked for data type mismatches and overflow. Conversion and overflow problems
result in warnings, or errors in strict SQL mode.
* Only scalar values can be assigned. For example, a statement such as SET x = (SELECT 1, 2) is
invalid.
* For character data types, if there is a CHARACTER SET attribute in the declaration, the specified
character set and its default collation is used. If the COLLATE attribute is also present, that collation is
used rather than the default collation.

If CHARACTER SET and COLLATE attributes are not present, the database character set and collation in
effect at routine creation time are used. To avoid having the server use the database character set and
collation, provide explicit CHARACTER SET and COLLATE attributes for character data parameters.
If you change the database default character set or collation, stored routines that use the database
defaults must be dropped and recreated so that they use the new defaults.

The database character set and collation are given by the value of the character_set_database
and collation_database system variables. For more information, see Section 10.3.3, “Database
Character Set and Collation”.

## 13.1.16 CREATE SERVER Syntax

```
CREATE SERVER server_name
    FOREIGN DATA WRAPPER wrapper_name
    OPTIONS (option [, option] ...)

option:
    { HOST character-literal
    | DATABASE character-literal
    | USER character-literal
    | PASSWORD character-literal
    | SOCKET character-literal
    | OWNER character-literal
    | PORT numeric-literal }
```

This statement creates the definition of a server for use with the FEDERATED storage engine. The CREATE
SERVER statement creates a new row in the servers table in the mysql database. This statement
requires the SUPER privilege.

The server_name should be a unique reference to the server. Server definitions are global within the
scope of the server, it is not possible to qualify the server definition to a specific database. server_name
has a maximum length of 64 characters (names longer than 64 characters are silently truncated), and is
case insensitive. You may specify the name as a quoted string.

The wrapper_name is an identifier and may be quoted with single quotation marks.

For each option you must specify either a character literal or numeric literal. Character literals are UTF-8,
support a maximum length of 64 characters and default to a blank (empty) string. String literals are silently
truncated to 64 characters. Numeric literals must be a number between 0 and 9999, default value is 0.

    Note
    The OWNER option is currently not applied, and has no effect on the ownership or
    operation of the server connection that is created.

The CREATE SERVER statement creates an entry in the mysql.servers table that can later be used with
the CREATE TABLE statement when creating a FEDERATED table. The options that you specify will be
used to populate the columns in the mysql.servers table. The table columns are Server_name, Host,
Db, Username, Password, Port and Socket.

For example:
```
CREATE SERVER s
FOREIGN DATA WRAPPER mysql
OPTIONS (USER 'Remote', HOST '198.51.100.106', DATABASE 'test');
```

Be sure to specify all options necessary to establish a connection to the server. The user name, host
name, and database name are mandatory. Other options might be required as well, such as password.

The data stored in the table can be used when creating a connection to a FEDERATED table:
```
CREATE TABLE t (s1 INT) ENGINE=FEDERATED CONNECTION='s';
```

For more information, see Section 16.8, “The FEDERATED Storage Engine”.

CREATE SERVER causes an implicit commit. See Section 13.3.3, “Statements That Cause an Implicit
Commit”.

CREATE SERVER is not written to the binary log, regardless of the logging format that is in use.

## 13.1.17 CREATE SPATIAL REFERENCE SYSTEM Syntax

```
CREATE OR REPLACE SPATIAL REFERENCE SYSTEM
    srid srs_attribute ...

CREATE SPATIAL REFERENCE SYSTEM
    [IF NOT EXISTS]
    srid srs_attribute ...

srs_attribute: {
    NAME 'srs_name'
    | DEFINITION 'definition'
    | ORGANIZATION 'org_name' IDENTIFIED BY org_id
    | DESCRIPTION 'description'
}

srid, org_id: 32-bit unsigned integer
```

This statement creates a spatial reference system (SRS) definition and stores it in the data dictionary. The
definition can be inspected using the INFORMATION_SCHEMA ST_SPATIAL_REFERENCE_SYSTEMS table.
This statement requires the SUPER privilege.

If neither OR REPLACE nor IF NOT EXISTS is specified, an error occurs if an SRS definition with the
SRID value already exists.

With CREATE OR REPLACE syntax, any existing SRS definition with the same SRID value is replaced,
unless the SRID value is used by some column in an existing table. In that case, an error occurs. For
example:
```
mysql> CREATE OR REPLACE SPATIAL REFERENCE SYSTEM 4326 ...;
ERROR 3716 (SR005): Can't modify SRID 4326. There is at
least one column depending on it.
```

To identify which column or columns use the SRID, use this query:
```
SELECT * FROM INFORMATION_SCHEMA.ST_GEOMETRY_COLUMNS WHERE SRS_ID=4326;
```

With CREATE ... IF NOT EXISTS syntax, any existing SRS definition with the same SRID value
causes the new definition to be ignored and a warning occurs.

SRID values must be in the range of 32-bit unsigned integers, with these restrictions:
* SRID 0 is a valid SRID but cannot be used with CREATE SPATIAL REFERENCE SYSTEM.
* If the value is in a reserved SRID range, a warning occurs. Reserved ranges are [0, 32767] (reserved by
EPSG), [60,000,000, 69,999,999] (reserved by EPSG), and [2,000,000,000, 2,147,483,647] (reserved by
MySQL). EPSG stands for the European Petroleum Survey Group.
* Users should not create SRSs with SRIDs in the reserved ranges. Doing so runs the risk that the SRIDs
will conflict with future SRS definitions distributed with MySQL, with the result that the new system-
provided SRSs are not installed for MySQL upgrades or that the user-defined SRSs are overwritten.

Attributes for the statement must satisfy these conditions:
* Attributes can be given in any order, but no attribute can be given more than once.
* The NAME and DEFINITION attributes are mandatory.
* The NAME srs_name attribute value must be unique. The combination of the ORGANIZATION
org_name and org_id attribute values must be unique.
* The NAME srs_name attribute value and ORGANIZATION org_name attribute value cannot be empty or
begin or end with whitespace.
* String values in attribute specifications cannot contain control characters, including newline.
* The following table shows the maximum lengths for string attribute values.

    Table 13.5 CREATE SPATIAL REFERENCE SYSTEM Attribute Lengths
    Attribute       Maximum Length (characters)
    NAME            80
    DEFINITION      4096
    ORGANIZATION    256
    DESCRIPTION     2048

Here is an example CREATE SPATIAL REFERENCE SYSTEM statement. The DEFINITION value is
reformatted across multiple lines for readability. (For the statement to be legal, the value actually must be
given on a single line.)
```
CREATE SPATIAL REFERENCE SYSTEM 4120
NAME 'Greek'
ORGANIZATION 'EPSG' IDENTIFIED BY 4120
DEFINITION
    'GEOGCS["Greek",DATUM["Greek",SPHEROID["Bessel 1841",
    6377397.155,299.1528128,AUTHORITY["EPSG","7004"]],
    AUTHORITY["EPSG","6120"]],PRIMEM["Greenwich",0,
    AUTHORITY["EPSG","8901"]],UNIT["degree",0.017453292519943278,
    AUTHORITY["EPSG","9122"]],AXIS["Lat",NORTH],AXIS["Lon",EAST],
    AUTHORITY["EPSG","4120"]]';
```

The grammar for SRS definitions is based on the grammar defined in OpenGIS Implementation
Specification: Coordinate Transformation Services, Revision 1.00, OGC 01-009, January 12, 2001, Section
7.2. This specification is available at http://www.opengeospatial.org/standards/ct.

MySQL incorporates these changes to the specification:
* Only the `<horz cs>` production rule is implemented (that is, geographic and projected SRSs).
* There is an optional, nonstandard `<authority>` clause for `<parameter>`. This makes it possible to
recognize projection parameters by authority instead of name.
* SRS definitions may not contain newlines.

## 13.1.18 CREATE TABLE Syntax

```
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    (create_definition,...)
    [table_options]
    [partition_options]

CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    [(create_definition,...)]
    [table_options]
    [partition_options]
    [IGNORE | REPLACE]
    [AS] query_expression

CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    { LIKE old_tbl_name | (LIKE old_tbl_name) }

create_definition:
    col_name column_definition
    | [CONSTRAINT [symbol]] PRIMARY KEY [index_type] (key_part,...)
        [index_option] ...
    | {INDEX|KEY} [index_name] [index_type] (key_part,...)
        [index_option] ...
    | [CONSTRAINT [symbol]] UNIQUE [INDEX|KEY]
        [index_name] [index_type] (key_part,...)
        [index_option] ...
    | {FULLTEXT|SPATIAL} [INDEX|KEY] [index_name] (key_part,...)
        [index_option] ...
    | [CONSTRAINT [symbol]] FOREIGN KEY
        [index_name] (col_name,...) reference_definition
    | CHECK (expr)

column_definition:
    data_type [NOT NULL | NULL] [DEFAULT {literal | (expr)} ]
        [AUTO_INCREMENT] [UNIQUE [KEY]] [[PRIMARY] KEY]
        [COMMENT 'string']
        [COLUMN_FORMAT {FIXED|DYNAMIC|DEFAULT}]
        [reference_definition]
    | data_type [GENERATED ALWAYS] AS (expression)
        [VIRTUAL | STORED] [NOT NULL | NULL]
        [UNIQUE [KEY]] [[PRIMARY] KEY]
        [COMMENT 'string']

data_type:
    (see Chapter 11, Data Types)

key_part: {col_name [(length)] | (expr)} [ASC | DESC]

index_type:
    USING {BTREE | HASH}

index_option:
    KEY_BLOCK_SIZE [=] value
    | index_type
    | WITH PARSER parser_name
    | COMMENT 'string'
    | {VISIBLE | INVISIBLE}

reference_definition:
    REFERENCES tbl_name (key_part,...)
        [MATCH FULL | MATCH PARTIAL | MATCH SIMPLE]
        [ON DELETE reference_option]
        [ON UPDATE reference_option]

reference_option:
    RESTRICT | CASCADE | SET NULL | NO ACTION | SET DEFAULT

table_options:
    table_option [[,] table_option] ...

table_option:
    AUTO_INCREMENT [=] value
    | AVG_ROW_LENGTH [=] value
    | [DEFAULT] CHARACTER SET [=] charset_name
    | CHECKSUM [=] {0 | 1}
    | [DEFAULT] COLLATE [=] collation_name
    | COMMENT [=] 'string'
    | COMPRESSION [=] {'ZLIB'|'LZ4'|'NONE'}
    | CONNECTION [=] 'connect_string'
    | {DATA|INDEX} DIRECTORY [=] 'absolute path to directory'
    | DELAY_KEY_WRITE [=] {0 | 1}
    | ENCRYPTION [=] {'Y' | 'N'}
    | ENGINE [=] engine_name
    | INSERT_METHOD [=] { NO | FIRST | LAST }
    | KEY_BLOCK_SIZE [=] value
    | MAX_ROWS [=] value
    | MIN_ROWS [=] value
    | PACK_KEYS [=] {0 | 1 | DEFAULT}
    | PASSWORD [=] 'string'
    | ROW_FORMAT [=] {DEFAULT|DYNAMIC|FIXED|COMPRESSED|REDUNDANT|COMPACT}
    | STATS_AUTO_RECALC [=] {DEFAULT|0|1}
    | STATS_PERSISTENT [=] {DEFAULT|0|1}
    | STATS_SAMPLE_PAGES [=] value
    | TABLESPACE tablespace_name
    | UNION [=] (tbl_name[,tbl_name]...)

partition_options:
    PARTITION BY
        { [LINEAR] HASH(expr)
        | [LINEAR] KEY [ALGORITHM={1|2}] (column_list)
        | RANGE{(expr) | COLUMNS(column_list)}
        | LIST{(expr) | COLUMNS(column_list)} }
    [PARTITIONS num]
    [SUBPARTITION BY
        { [LINEAR] HASH(expr)
        | [LINEAR] KEY [ALGORITHM={1|2}] (column_list) }
      [SUBPARTITIONS num]
    ]
    [(partition_definition [, partition_definition] ...)]

partition_definition:
    PARTITION partition_name
    [VALUES
        {LESS THAN {(expr | value_list) | MAXVALUE}
        |
        IN (value_list)}]
    [[STORAGE] ENGINE [=] engine_name]
    [COMMENT [=] 'string' ]
    [DATA DIRECTORY [=] 'data_dir']
    [INDEX DIRECTORY [=] 'index_dir']
    [MAX_ROWS [=] max_number_of_rows]
    [MIN_ROWS [=] min_number_of_rows]
    [TABLESPACE [=] tablespace_name]
    [(subpartition_definition [, subpartition_definition] ...)]

subpartition_definition:
    SUBPARTITION logical_name
        [[STORAGE] ENGINE [=] engine_name]
        [COMMENT [=] 'string' ]
        [DATA DIRECTORY [=] 'data_dir']
        [INDEX DIRECTORY [=] 'index_dir']
        [MAX_ROWS [=] max_number_of_rows]
        [MIN_ROWS [=] min_number_of_rows]
        [TABLESPACE [=] tablespace_name]

query_expression:
    SELECT ... (Some valid select or union statement)
```

CREATE TABLE creates a table with the given name. You must have the CREATE privilege for the table.

By default, tables are created in the default database, using the InnoDB storage engine. An error occurs if
the table exists, if there is no default database, or if the database does not exist.

For information about the physical representation of a table, see Section 13.1.18.2, “Files Created by
CREATE TABLE”.

The original CREATE TABLE statement, including all specifications and table options are stored by MySQL
when the table is created. For more information, see Section 13.1.18.1, “CREATE TABLE Statement
Retention”.

There are several aspects to the CREATE TABLE statement, described under the following topics in this
section:
* Table Name
* Temporary Tables
* Cloning or Copying a Table
* Column Data Types and Attributes
* Indexes and Foreign Keys
* Table Options
* Creating Partitioned Tables

### Table Name

• tbl_name

The table name can be specified as db_name.tbl_name to create the table in a specific database.
This works regardless of whether there is a default database, assuming that the database exists.
If you use quoted identifiers, quote the database and table names separately. For example, write
`mydb`.`mytbl`, not `mydb.mytbl`.

Rules for permissible table names are given in Section 9.2, “Schema Object Names”.

• IF NOT EXISTS

Prevents an error from occurring if the table exists. However, there is no verification that the existing
table has a structure identical to that indicated by the CREATE TABLE statement.

### Temporary Tables

You can use the TEMPORARY keyword when creating a table. A TEMPORARY table is visible only within
the current session, and is dropped automatically when the session is closed. For more information, see
Section 13.1.18.3, “CREATE TEMPORARY TABLE Syntax”.

### Cloning or Copying a Table

• LIKE

Use CREATE TABLE ... LIKE to create an empty table based on the definition of another table,
including any column attributes and indexes defined in the original table:
```
CREATE TABLE new_tbl LIKE orig_tbl;
```
For more information, see Section 13.1.18.4, “CREATE TABLE ... LIKE Syntax”.

• [AS] query_expression

To create one table from another, add a SELECT statement at the end of the CREATE TABLE statement:
```
CREATE TABLE new_tbl AS SELECT * FROM orig_tbl;
```
For more information, see Section 13.1.18.5, “CREATE TABLE ... SELECT Syntax”.

• IGNORE|REPLACE

The IGNORE and REPLACE options indicate how to handle rows that duplicate unique key values when
copying a table using a SELECT statement.
For more information, see Section 13.1.18.5, “CREATE TABLE ... SELECT Syntax”.

### Column Data Types and Attributes

There is a hard limit of 4096 columns per table, but the effective maximum may be less for a given table
and depends on the factors discussed in Section C.10.4, “Limits on Table Column Count and Row Size”.

* data_type

  data_type represents the data type in a column definition. For a full description of the syntax
  available for specifying column data types, as well as information about the properties of each type, see
  Chapter 11, Data Types.

  * Some attributes do not apply to all data types. AUTO_INCREMENT applies only to integer and floating-
  point types. Prior to MySQL 8.0.13, DEFAULT does not apply to the BLOB, TEXT, GEOMETRY, and
  JSON types.

  * Character data types (CHAR, VARCHAR, TEXT) can include CHARACTER SET and COLLATE attributes
  to specify the character set and collation for the column. For details, see Chapter 10, Character Sets,
  Collations, Unicode. CHARSET is a synonym for CHARACTER SET. Example:
  ```
  CREATE TABLE t (c CHAR(20) CHARACTER SET utf8 COLLATE utf8_bin);
  ```
  MySQL 8.0 interprets length specifications in character column definitions in characters. Lengths for
  BINARY and VARBINARY are in bytes.

  * For CHAR, VARCHAR, BINARY, and VARBINARY columns, indexes can be created that use only the
    leading part of column values, using col_name(length) syntax to specify an index prefix length.
    BLOB and TEXT columns also can be indexed, but a prefix length must be given. Prefix lengths are
    given in characters for nonbinary string types and in bytes for binary string types. That is, index entries
    consist of the first length characters of each column value for CHAR, VARCHAR, and TEXT columns,
    and the first length bytes of each column value for BINARY, VARBINARY, and BLOB columns.

    Indexing only a prefix of column values like this can make the index file much smaller. For additional
    information about index prefixes, see Section 13.1.14, “CREATE INDEX Syntax”.

    Only the InnoDB and MyISAM storage engines support indexing on BLOB and TEXT columns. For
    example:
    ```
    CREATE TABLE test (blob_col BLOB, INDEX(blob_col(10)));
    ```

    If a specified index prefix exceeds the maximum column data type size, CREATE TABLE handles the
    index as follows:

    * For a nonunique index, either an error occurs (if strict SQL mode is enabled), or the index length is
    reduced to lie within the maximum column data type size and a warning is produced (if strict SQL
    mode is not enabled).

    * For a unique index, an error occurs regardless of SQL mode because reducing the index length
    might enable insertion of nonunique entries that do not meet the specified uniqueness requirement.

    * JSON columns cannot be indexed. You can work around this restriction by creating an index on a
    generated column that extracts a scalar value from the JSON column. See Indexing a Generated
    Column to Provide a JSON Column Index, for a detailed example.

* NOT NULL | NULL

 If neither NULL nor NOT NULL is specified, the column is treated as though NULL had been specified.
 In MySQL 8.0, only the InnoDB, MyISAM, and MEMORY storage engines support indexes on columns
 that can have NULL values. In other cases, you must declare indexed columns as NOT NULL or an error
 results.

* DEFAULT

  Specifies a default value for a column. For more information about default value handling, including the
  case that a column definition includes no explicit DEFAULT value, see Section 11.7, “Data Type Default
  Values”.
  
  If the NO_ZERO_DATE or NO_ZERO_IN_DATE SQL mode is enabled and a date-valued default is not
  correct according to that mode, CREATE TABLE produces a warning if strict SQL mode is not enabled
  and an error if strict mode is enabled. For example, with NO_ZERO_IN_DATE enabled, c1 DATE
  DEFAULT '2010-00-00' produces a warning.

* AUTO_INCREMENT

  An integer or floating-point column can have the additional attribute AUTO_INCREMENT. When you insert
  a value of NULL (recommended) or 0 into an indexed AUTO_INCREMENT column, the column is set to
  the next sequence value. Typically this is value+1, where value is the largest value for the column
  currently in the table. AUTO_INCREMENT sequences begin with 1.

  To retrieve an AUTO_INCREMENT value after inserting a row, use the LAST_INSERT_ID() SQL
  function or the mysql_insert_id() C API function. See Section 12.14, “Information Functions”, and
  Section 27.7.7.38, “mysql_insert_id()”.

  If the NO_AUTO_VALUE_ON_ZERO SQL mode is enabled, you can store 0 in AUTO_INCREMENT columns
  as 0 without generating a new sequence value. See Section 5.1.10, “Server SQL Modes”.

  There can be only one AUTO_INCREMENT column per table, it must be indexed, and it cannot have a
  DEFAULT value. An AUTO_INCREMENT column works properly only if it contains only positive values.
  Inserting a negative number is regarded as inserting a very large positive number. This is done to avoid
  precision problems when numbers “wrap” over from positive to negative and also to ensure that you do
  not accidentally get an AUTO_INCREMENT column that contains 0.

  For MyISAM tables, you can specify an AUTO_INCREMENT secondary column in a multiple-column key.
  See Section 3.6.9, “Using AUTO_INCREMENT”.

  To make MySQL compatible with some ODBC applications, you can find the AUTO_INCREMENT value
  for the last inserted row with the following query:
  ```
  SELECT * FROM tbl_name WHERE auto_col IS NULL
  ```

  This method requires that sql_auto_is_null variable is not set to 0. See Section 5.1.7, “Server
  System Variables”.

  For information about InnoDB and AUTO_INCREMENT, see Section 15.8.1.5, “AUTO_INCREMENT
  Handling in InnoDB”. For information about AUTO_INCREMENT and MySQL Replication, see
  Section 17.4.1.1, “Replication and AUTO_INCREMENT”.

* COMMENT

  A comment for a column can be specified with the COMMENT option, up to 1024 characters long. The
  comment is displayed by the SHOW CREATE TABLE and SHOW FULL COLUMNS statements.

* COLUMN_FORMAT

  Used by MySQL Cluster to determine a column's storage format. This option currently has no effect on
  columns of tables using storage engines other than NDB. In MySQL 8.0 and later, COLUMN_FORMAT is
  silently ignored.

* GENERATED ALWAYS

  Used to specify a generated column expression. For information about generated columns, see
  Section 13.1.18.8, “CREATE TABLE and Generated Columns”.

  Stored generated columns can be indexed. InnoDB supports secondary indexes on virtual generated
  columns. See Section 13.1.18.9, “Secondary Indexes and Generated Columns”.

### Indexes and Foreign Keys

Several keywords apply to creation of indexes and foreign keys. For general background in addition to
the following descriptions, see Section 13.1.14, “CREATE INDEX Syntax”, and Section 13.1.18.6, “Using
FOREIGN KEY Constraints”.

• CONSTRAINT symbol

If the CONSTRAINT symbol clause is given, the symbol value, if used, must be unique in the database.
A duplicate symbol results in an error. If the clause is not given, or a symbol is not included following
the CONSTRAINT keyword, a name for the constraint is created automatically.

• PRIMARY KEY

A unique index where all key columns must be defined as NOT NULL. If they are not explicitly declared
as NOT NULL, MySQL declares them so implicitly (and silently). A table can have only one PRIMARY
KEY. The name of a PRIMARY KEY is always PRIMARY, which thus cannot be used as the name for any
other kind of index.

If you do not have a PRIMARY KEY and an application asks for the PRIMARY KEY in your tables,
MySQL returns the first UNIQUE index that has no NULL columns as the PRIMARY KEY.
In InnoDB tables, keep the PRIMARY KEY short to minimize storage overhead for secondary indexes.
Each secondary index entry contains a copy of the primary key columns for the corresponding row. (See
Section 15.8.2.1, “Clustered and Secondary Indexes”.)

In the created table, a PRIMARY KEY is placed first, followed by all UNIQUE indexes, and then the
nonunique indexes. This helps the MySQL optimizer to prioritize which index to use and also more
quickly to detect duplicated UNIQUE keys.

A PRIMARY KEY can be a multiple-column index. However, you cannot create a multiple-column index
using the PRIMARY KEY key attribute in a column specification. Doing so only marks that single column
as primary. You must use a separate PRIMARY KEY(key_part, ...) clause.

If a table has a PRIMARY KEY or UNIQUE NOT NULL index that consists of a single column that has an
integer type, you can use _rowid to refer to the indexed column in SELECT statements, as described in
Unique Indexes.

In MySQL, the name of a PRIMARY KEY is PRIMARY. For other indexes, if you do not assign a name,
the index is assigned the same name as the first indexed column, with an optional suffix (_2, _3, ...)
to make it unique. You can see index names for a table using SHOW INDEX FROM tbl_name. See
Section 13.7.6.22, “SHOW INDEX Syntax”.

• KEY | INDEX

KEY is normally a synonym for INDEX. The key attribute PRIMARY KEY can also be specified as just
KEY when given in a column definition. This was implemented for compatibility with other database
systems.

• UNIQUE

A UNIQUE index creates a constraint such that all values in the index must be distinct. An error occurs
if you try to add a new row with a key value that matches an existing row. For all engines, a UNIQUE
index permits multiple NULL values for columns that can contain NULL. If you specify a prefix value for a
column in a UNIQUE index, the column values must be unique within the prefix length.

If a table has a PRIMARY KEY or UNIQUE NOT NULL index that consists of a single column that has an
integer type, you can use _rowid to refer to the indexed column in SELECT statements, as described in
Unique Indexes.

• FULLTEXT

A FULLTEXT index is a special type of index used for full-text searches. Only the InnoDB and MyISAM
storage engines support FULLTEXT indexes. They can be created only from CHAR, VARCHAR, and TEXT
columns. Indexing always happens over the entire column; column prefix indexing is not supported and
any prefix length is ignored if specified. See Section 12.9, “Full-Text Search Functions”, for details of
operation. A WITH PARSER clause can be specified as an index_option value to associate a parser
plugin with the index if full-text indexing and searching operations need special handling. This clause is
valid only for FULLTEXT indexes. InnoDB and MyISAM support full-text parser plugins. See Full-Text
Parser Plugins and Section 28.2.4.4, “Writing Full-Text Parser Plugins” for more information.

• SPATIAL

You can create SPATIAL indexes on spatial data types. Spatial types are supported only for InnoDB
and MyISAM tables, and indexed columns must be declared as NOT NULL. See Section 11.5, “Spatial
Data Types”.

• FOREIGN KEY

MySQL supports foreign keys, which let you cross-reference related data across tables, and foreign key
constraints, which help keep this spread-out data consistent. For definition and option information, see
reference_definition, and reference_option.

Partitioned tables employing the InnoDB storage engine do not support foreign keys. See Section 22.6,
“Restrictions and Limitations on Partitioning”, for more information.

• CHECK

The CHECK clause is parsed but ignored by all storage engines. See Section 1.8.2.3, “Foreign Key
Differences”.

• key_part

  • A key_part specification can end with ASC or DESC to specify whether index values are stored in
  ascending or descending order. The default is ascending if no order specifier is given.

  • Prefixes, defined by the length attribute, can be up to 767 bytes long for InnoDB tables that use the
  REDUNDANT or COMPACT row format. The prefix length limit is 3072 bytes for InnoDB tables that use
  the DYNAMIC or COMPRESSED row format. For MyISAM tables, the prefix length limit is 1000 bytes.
  Prefix limits are measured in bytes. However, prefix lengths for index specifications in CREATE
  TABLE, ALTER TABLE, and CREATE INDEX statements are interpreted as number of characters for
  nonbinary string types (CHAR, VARCHAR, TEXT) and number of bytes for binary string types (BINARY,
  VARBINARY, BLOB). Take this into account when specifying a prefix length for a nonbinary string
  column that uses a multibyte character set.

• index_type

  Some storage engines permit you to specify an index type when creating an index. The syntax for the
  index_type specifier is USING type_name.
  
  Example:
  ```
  CREATE TABLE lookup
  (id INT, INDEX USING BTREE (id))
  ENGINE = MEMORY;
  ```
  
  The preferred position for USING is after the index column list. It can be given before the column list,
  but support for use of the option in that position is deprecated and will be removed in a future MySQL
  release.
  
• index_option

  index_option values specify additional options for an index.

  • KEY_BLOCK_SIZE

  For MyISAM tables, KEY_BLOCK_SIZE optionally specifies the size in bytes to use for index key
  blocks. The value is treated as a hint; a different size could be used if necessary. A KEY_BLOCK_SIZE
  value specified for an individual index definition overrides the table-level KEY_BLOCK_SIZE value.
  For information about the table-level KEY_BLOCK_SIZE attribute, see Table Options.

  • WITH PARSER

  The WITH PARSER option can only be used with FULLTEXT indexes. It associates a parser plugin with
  the index if full-text indexing and searching operations need special handling. InnoDB and MyISAM
  support full-text parser plugins. If you have a MyISAM table with an associated full-text parser plugin,
  you can convert the table to InnoDB using ALTER TABLE.

  • COMMENT

  In MySQL 8.0, index definitions can include an optional comment of up to 1024 characters.
  You can set the InnoDB MERGE_THRESHOLD value for an individual index using the index_option
  COMMENT clause. See Section 15.6.12, “Configuring the Merge Threshold for Index Pages”.
  For more information about permissible index_option values, see Section 13.1.14, “CREATE INDEX
  Syntax”. For more information about indexes, see Section 8.3.1, “How MySQL Uses Indexes”.

• reference_definition

  For reference_definition syntax details and examples, see Section 13.1.18.6, “Using FOREIGN
  KEY Constraints”. For information specific to foreign keys in InnoDB, see Section 15.8.1.6, “InnoDB and
  FOREIGN KEY Constraints”.
  
  InnoDB tables support checking of foreign key constraints. The columns of the referenced table must
  always be explicitly named. Both ON DELETE and ON UPDATE actions on foreign keys. For more
  detailed information and examples, see Section 13.1.18.6, “Using FOREIGN KEY Constraints”. For
  information specific to foreign keys in InnoDB, see Section 15.8.1.6, “InnoDB and FOREIGN KEY
  Constraints”.

  For other storage engines, MySQL Server parses and ignores the FOREIGN KEY and REFERENCES
  syntax in CREATE TABLE statements. See Section 1.8.2.3, “Foreign Key Differences”.


    Important

    For users familiar with the ANSI/ISO SQL Standard, please note that no storage
    engine, including InnoDB, recognizes or enforces the MATCH clause used in
    referential integrity constraint definitions. Use of an explicit MATCH clause will not
    have the specified effect, and also causes ON DELETE and ON UPDATE clauses
    to be ignored. For these reasons, specifying MATCH should be avoided.

    The MATCH clause in the SQL standard controls how NULL values in a composite
    (multiple-column) foreign key are handled when comparing to a primary key.
    InnoDB essentially implements the semantics defined by MATCH SIMPLE, which
    permit a foreign key to be all or partially NULL. In that case, the (child table) row
    containing such a foreign key is permitted to be inserted, and does not match any
    row in the referenced (parent) table. It is possible to implement other semantics
    using triggers.

    Additionally, MySQL requires that the referenced columns be indexed for
    performance. However, InnoDB does not enforce any requirement that the
    referenced columns be declared UNIQUE or NOT NULL. The handling of foreign
    key references to nonunique keys or keys that contain NULL values is not well
    defined for operations such as UPDATE or DELETE CASCADE. You are advised
    to use foreign keys that reference only keys that are both UNIQUE (or PRIMARY)
    and NOT NULL.

    MySQL parses but ignores “inline REFERENCES specifications” (as defined
    in the SQL standard) where the references are defined as part of the column
    specification. MySQL accepts REFERENCES clauses only when specified as part
    of a separate FOREIGN KEY specification.

• reference_option

For information about the RESTRICT, CASCADE, SET NULL, NO ACTION, and SET DEFAULT options,
see Section 13.1.18.6, “Using FOREIGN KEY Constraints”.

### Table Options

Table options are used to optimize the behavior of the table. In most cases, you do not have to specify any
of them. These options apply to all storage engines unless otherwise indicated. Options that do not apply
to a given storage engine may be accepted and remembered as part of the table definition. Such options
then apply if you later use ALTER TABLE to convert the table to use a different storage engine.

• ENGINE

Specifies the storage engine for the table, using one of the names shown in the following table. The
engine name can be unquoted or quoted. The quoted name 'DEFAULT' is recognized but ignored.

    Storage Engine          Description
    -------------------------------------
    InnoDB
                            Transaction-safe tables with row locking and foreign keys. The default
                            storage engine for new tables. See Chapter 15, The InnoDB Storage
                            Engine, and in particular Section 15.1, “Introduction to InnoDB” if you
                            have MySQL experience but are new to InnoDB.
    MyISAM
                            The binary portable storage engine that is primarily used for read-only
                            or read-mostly workloads. See Section 16.2, “The MyISAM Storage
                            Engine”.
    MEMORY
                            The data for this storage engine is stored only in memory. See
                            Section 16.3, “The MEMORY Storage Engine”.
    CSV
                            Tables that store rows in comma-separated values format. See
                            Section 16.4, “The CSV Storage Engine”.
    ARCHIVE
                            The archiving storage engine. See Section 16.5, “The ARCHIVE
                            Storage Engine”.
    EXAMPLE
                            An example engine. See Section 16.9, “The EXAMPLE Storage
                            Engine”.
    FEDERATED
                            Storage engine that accesses remote tables. See Section 16.8, “The
                            FEDERATED Storage Engine”.
    HEAP                    
                            This is a synonym for MEMORY.
    MERGE
                            A collection of MyISAM tables used as one table. Also known as
                            MRG_MyISAM. See Section 16.7, “The MERGE Storage Engine”.

  By default, if a storage engine is specified that is not available, the statement fails with an error. You
  can override this behavior by removing NO_ENGINE_SUBSTITUTION from the server SQL mode (see
  Section 5.1.10, “Server SQL Modes”) so that MySQL allows substitution of the specified engine with the
  default storage engine instead. Normally in such cases, this is InnoDB, which is the default value for
  the default_storage_engine system variable. When NO_ENGINE_SUBSTITUTION is disabled, a
  warning occurs if the storage engine specification is not honored.

• AUTO_INCREMENT

  The initial AUTO_INCREMENT value for the table. In MySQL 8.0, this works for MyISAM, MEMORY,
  InnoDB, and ARCHIVE tables. To set the first auto-increment value for engines that do not support the
  AUTO_INCREMENT table option, insert a “dummy” row with a value one less than the desired value after
  creating the table, and then delete the dummy row.
  
  For engines that support the AUTO_INCREMENT table option in CREATE TABLE statements, you can
  also use ALTER TABLE tbl_name AUTO_INCREMENT = N to reset the AUTO_INCREMENT value. The
  value cannot be set lower than the maximum value currently in the column.

• AVG_ROW_LENGTH

  An approximation of the average row length for your table. You need to set this only for large tables with
  variable-size rows.
  
  When you create a MyISAM table, MySQL uses the product of the MAX_ROWS and AVG_ROW_LENGTH
  options to decide how big the resulting table is. If you don't specify either option, the maximum size
  for MyISAM data and index files is 256TB by default. (If your operating system does not support files
  that large, table sizes are constrained by the file size limit.) If you want to keep down the pointer sizes
  to make the index smaller and faster and you don't really need big files, you can decrease the default
  pointer size by setting the myisam_data_pointer_size system variable. (See Section 5.1.7, “Server
  System Variables”.) If you want all your tables to be able to grow above the default limit and are willing to
  have your tables slightly slower and larger than necessary, you can increase the default pointer size by
  setting this variable. Setting the value to 7 permits table sizes up to 65,536TB.

• [DEFAULT] CHARACTER SET

Specifies a default character set for the table. CHARSET is a synonym for CHARACTER SET. If the
character set name is DEFAULT, the database character set is used.

• CHECKSUM

Set this to 1 if you want MySQL to maintain a live checksum for all rows (that is, a checksum that MySQL
updates automatically as the table changes). This makes the table a little slower to update, but also
makes it easier to find corrupted tables. The CHECKSUM TABLE statement reports the checksum.
(MyISAM only.)

• [DEFAULT] COLLATE

Specifies a default collation for the table.

• COMMENT

A comment for the table, up to 2048 characters long.
You can set the InnoDB MERGE_THRESHOLD value for a table using the table_option COMMENT
clause. See Section 15.6.12, “Configuring the Merge Threshold for Index Pages”.

• COMPRESSION

The compression algorithm used for page level compression for InnoDB tables. Supported values
include Zlib, LZ4, and None. The COMPRESSION attribute was introduced with the transparent page
compression feature. Page compression is only supported with InnoDB tables that reside in file-per-
table tablespaces, and is only available on Linux and Windows platforms that support sparse files and
hole punching. For more information, see Section 15.9.2, “InnoDB Page Compression”.

• CONNECTION

The connection string for a FEDERATED table.

    Note
    Older versions of MySQL used a COMMENT option for the connection string.

• DATA DIRECTORY, INDEX DIRECTORY

For InnoDB, the DATA DIRECTORY='directory' option allows you to create InnoDB file-per-table
tablespaces outside the MySQL data directory. Within the directory that you specify, MySQL creates
a subdirectory corresponding to the database name, and within that a .ibd file for the table. The
innodb_file_per_table configuration option must be enabled to use the DATA DIRECTORY option
with InnoDB. The full directory path must be specified. See Section 15.7.5, “Creating File-Per-Table
Tablespaces Outside the Data Directory” for more information.

When creating MyISAM tables, you can use the DATA DIRECTORY='directory' clause, the INDEX
DIRECTORY='directory' clause, or both. They specify where to put a MyISAM table's data file and
index file, respectively. Unlike InnoDB tables, MySQL does not create subdirectories that correspond
to the database name when creating a MyISAM table with a DATA DIRECTORY or INDEX DIRECTORY
option. Files are created in the directory that is specified.

You must have the FILE privilege to use the DATA DIRECTORY or INDEX DIRECTORY table option.

    Important
    Table-level DATA DIRECTORY and INDEX DIRECTORY options are ignored for
    partitioned tables. (Bug #32091)

These options work only when you are not using the --skip-symbolic-links option. Your operating
system must also have a working, thread-safe realpath() call. See Section 8.12.2.2, “Using Symbolic
Links for MyISAM Tables on Unix”, for more complete information.

If a MyISAM table is created with no DATA DIRECTORY option, the .MYD file is created in the database
directory. By default, if MyISAM finds an existing .MYD file in this case, it overwrites it. The same applies
to .MYI files for tables created with no INDEX DIRECTORY option. To suppress this behavior, start the
server with the --keep_files_on_create option, in which case MyISAM will not overwrite existing
files and returns an error instead.

If a MyISAM table is created with a DATA DIRECTORY or INDEX DIRECTORY option and an existing
.MYD or .MYI file is found, MyISAM always returns an error. It will not overwrite a file in the specified
directory.

    Important
    You cannot use path names that contain the MySQL data directory with DATA
    DIRECTORY or INDEX DIRECTORY. This includes partitioned tables and
    individual table partitions. (See Bug #32167.)

• DELAY_KEY_WRITE

Set this to 1 if you want to delay key updates for the table until the table is closed. See the description of
the delay_key_write system variable in Section 5.1.7, “Server System Variables”. (MyISAM only.)

• ENCRYPTION

Set the ENCRYPTION option to 'Y' to enable page-level data encryption for an InnoDB table created
in a file-per-table tablespace. Option values are not case-sensitive. The ENCRYPTION option was
introduced with the InnoDB tablespace encryption feature; see Section 15.7.11, “InnoDB Tablespace
Encryption”. The keyring_file plugin must be loaded to use the ENCRYPTION option.

• INSERT_METHOD

If you want to insert data into a MERGE table, you must specify with INSERT_METHOD the table into which
the row should be inserted. INSERT_METHOD is an option useful for MERGE tables only. Use a value
of FIRST or LAST to have inserts go to the first or last table, or a value of NO to prevent inserts. See
Section 16.7, “The MERGE Storage Engine”.

• KEY_BLOCK_SIZE

For MyISAM tables, KEY_BLOCK_SIZE optionally specifies the size in bytes to use for index key blocks.
The value is treated as a hint; a different size could be used if necessary. A KEY_BLOCK_SIZE value
specified for an individual index definition overrides the table-level KEY_BLOCK_SIZE value.

For InnoDB tables, KEY_BLOCK_SIZE optionally specifies the page size (in kilobytes) to use for
compressed InnoDB tables. The KEY_BLOCK_SIZE value is treated as a hint; a different size
could be used by InnoDB if necessary. KEY_BLOCK_SIZE can only be less than or equal to the
innodb_page_size value. A value of 0 represents the default compressed page size, which is half
of the innodb_page_size value. Depending on innodb_page_size, possible KEY_BLOCK_SIZE
values include 0, 1, 2, 4, 8, and 16. See Section 15.9.1, “InnoDB Table Compression” for more
information.

Oracle recommends enabling innodb_strict_mode when specifying KEY_BLOCK_SIZE for InnoDB
tables. When innodb_strict_mode is enabled, specifying an invalid KEY_BLOCK_SIZE value returns
an error. If innodb_strict_mode is disabled, an invalid KEY_BLOCK_SIZE value results in a warning,
and the KEY_BLOCK_SIZE option is ignored.

The Create_options column in response to SHOW TABLE STATUS reports the actual
KEY_BLOCK_SIZE used by the table, as does SHOW CREATE TABLE.

InnoDB only supports KEY_BLOCK_SIZE at the table level.

KEY_BLOCK_SIZE is not supported with 32k and 64k innodb_page_size values. InnoDB table
compression does not support these pages sizes.

InnoDB does not support the KEY_BLOCK_SIZE option when creating temporary tables.

• MAX_ROWS

The maximum number of rows you plan to store in the table. This is not a hard limit, but rather a hint to
the storage engine that the table must be able to store at least this many rows.
The maximum MAX_ROWS value is 4294967295; larger values are truncated to this limit.

• MIN_ROWS

The minimum number of rows you plan to store in the table. The MEMORY storage engine uses this option
as a hint about memory use.

• PACK_KEYS

Takes effect only with MyISAM tables. Set this option to 1 if you want to have smaller indexes.
This usually makes updates slower and reads faster. Setting the option to 0 disables all packing of
keys. Setting it to DEFAULT tells the storage engine to pack only long CHAR, VARCHAR, BINARY, or
VARBINARY columns.

If you do not use PACK_KEYS, the default is to pack strings, but not numbers. If you use PACK_KEYS=1,
numbers are packed as well.

When packing binary number keys, MySQL uses prefix compression:
• Every key needs one extra byte to indicate how many bytes of the previous key are the same for the
next key.
• The pointer to the row is stored in high-byte-first order directly after the key, to improve compression.

This means that if you have many equal keys on two consecutive rows, all following “same” keys usually
only take two bytes (including the pointer to the row). Compare this to the ordinary case where the
following keys takes storage_size_for_key + pointer_size (where the pointer size is usually
4). Conversely, you get a significant benefit from prefix compression only if you have many numbers that
are the same. If all keys are totally different, you use one byte more per key, if the key is not a key that
can have NULL values. (In this case, the packed key length is stored in the same byte that is used to
mark if a key is NULL.)

• PASSWORD

This option is unused.

• ROW_FORMAT

  Defines the physical format in which the rows are stored.

  When executing a CREATE TABLE statement with strict mode disabled, if you specify a row format that
  is not supported by the storage engine that is used for the table, the table is created using that storage
  engine's default row format. The actual row format of the table is reported in the Row_format and
  Create_options columns in response to SHOW TABLE STATUS. SHOW CREATE TABLE also reports
  the actual row format of the table.

  Row format choices differ depending on the storage engine used for the table.

  For InnoDB tables:
  • The default row format is defined by innodb_default_row_format, which has a default setting
  of DYNAMIC. The default row format is used when the ROW_FORMAT option is not defined or when
  ROW_FORMAT=DEFAULT is used.

  If the ROW_FORMAT option is not defined, or if ROW_FORMAT=DEFAULT is used, operations
  that rebuild a table also silently change the row format of the table to the default defined by
  innodb_default_row_format. For more information, see Section 15.10.2, “Specifying the Row
  Format for a Table”.

  • For more efficient InnoDB storage of data types, especially BLOB types, use the DYNAMIC. See
  Section 15.10.3, “DYNAMIC and COMPRESSED Row Formats” for requirements associated with the
  DYNAMIC row format.

  • To enable compression for InnoDB tables, specify ROW_FORMAT=COMPRESSED. The
  ROW_FORMAT=COMPRESSED option is not supported when creating temporary tables. See
  Section 15.9, “InnoDB Table and Page Compression” for requirements associated with the
  COMPRESSED row format.

  • The row format used in older versions of MySQL can still be requested by specifying the REDUNDANT
  row format.

  • When you specify a non-default ROW_FORMAT clause, consider also enabling the
  innodb_strict_mode configuration option.

  • ROW_FORMAT=FIXED is not supported. If ROW_FORMAT=FIXED is specified while
  innodb_strict_mode is disabled, InnoDB issues a warning and assumes ROW_FORMAT=DYNAMIC.

  If ROW_FORMAT=FIXED is specified while innodb_strict_mode is enabled, which is the default,
  InnoDB returns an error.

  • For additional information about InnoDB row formats, see Section 15.10, “InnoDB Row Storage and
  Row Formats”.

  For MyISAM tables, the option value can be FIXED or DYNAMIC for static or variable-length row format.
  myisampack sets the type to COMPRESSED. See Section 16.2.3, “MyISAM Table Storage Formats”.

• STATS_AUTO_RECALC

Specifies whether to automatically recalculate persistent statistics for an InnoDB table. The
value DEFAULT causes the persistent statistics setting for the table to be determined by the
innodb_stats_auto_recalc configuration option. The value 1 causes statistics to be recalculated
when 10% of the data in the table has changed. The value 0 prevents automatic recalculation for this
table; with this setting, issue an ANALYZE TABLE statement to recalculate the statistics after making
substantial changes to the table. For more information about the persistent statistics feature, see
Section 15.6.11.1, “Configuring Persistent Optimizer Statistics Parameters”.
• STATS_PERSISTENT
Specifies whether to enable persistent statistics for an InnoDB table. The value DEFAULT causes
the persistent statistics setting for the table to be determined by the innodb_stats_persistent
configuration option. The value 1 enables persistent statistics for the table, while the value 0 turns off
this feature. After enabling persistent statistics through a CREATE TABLE or ALTER TABLE statement,
issue an ANALYZE TABLE statement to calculate the statistics, after loading representative data into the
table. For more information about the persistent statistics feature, see Section 15.6.11.1, “Configuring
Persistent Optimizer Statistics Parameters”.
• STATS_SAMPLE_PAGES
The number of index pages to sample when estimating cardinality and other statistics for an indexed
column, such as those calculated by ANALYZE TABLE. For more information, see Section 15.6.11.1,
“Configuring Persistent Optimizer Statistics Parameters”.
• TABLESPACE
The TABLESPACE option can be used to create a table in an existing general tablespace, a file-per-table
tablespace, or the system tablespace.
CREATE TABLE tbl_name ... TABLESPACE [=] tablespace_name
The general tablespace that you specify must exist prior to using the TABLESPACE option. For
information about general tablespaces, see Section 15.7.10, “InnoDB General Tablespaces”.
The tablespace_name is a case-sensitive identifier. It may be quoted or unquoted. The forward slash
character (“/”) is not permitted. Names beginning with “innodb_” are reserved for special use.
To create a table in the system tablespace, specify innodb_system as the tablespace name.
CREATE TABLE tbl_name ... TABLESPACE [=] innodb_system
Using the TABLESPACE [=] innodb_system option, you can place a table of any uncompressed row
format in the system tablespace regardless of the innodb_file_per_table setting. For example,
you can add a table with ROW_FORMAT=DYNAMIC to the system tablespace using the TABLESPACE [=]
innodb_system option.
To create a table in a file-per-table tablespace, specify innodb_file_per_table as the tablespace
name.
CREATE TABLE tbl_name ... TABLESPACE [=] innodb_file_per_table

    Note
    If innodb_file_per_table is enabled, you need not specify
    TABLESPACE=innodb_file_per_table to create an InnoDB file-per-table
    tablespace. InnoDB tables are created in file-per-table tablespaces by default
    when innodb_file_per_table is enabled.

The DATA DIRECTORY clause is permitted with CREATE TABLE ...

TABLESPACE=innodb_file_per_table but is otherwise not supported for use in combination with
the TABLESPACE option.

The TABLESPACE option is supported with ALTER TABLE, which can be used to move tables from one
tablespace to another. For more information, see Section 15.7.10, “InnoDB General Tablespaces”.

• UNION

Used to access a collection of identical MyISAM tables as one. This works only with MERGE tables. See
Section 16.7, “The MERGE Storage Engine”.

You must have SELECT, UPDATE, and DELETE privileges for the tables you map to a MERGE table.

    Note
    Formerly, all tables used had to be in the same database as the MERGE table
    itself. This restriction no longer applies.

### Creating Partitioned Tables

partition_options can be used to control partitioning of the table created with CREATE TABLE.

Not all options shown in the syntax for partition_options at the beginning of this section are available
for all partitioning types. Please see the listings for the following individual types for information specific to
each type, and see Chapter 22, Partitioning, for more complete information about the workings of and uses
for partitioning in MySQL, as well as additional examples of table creation and other statements relating to
MySQL partitioning.

Partitions can be modified, merged, added to tables, and dropped from tables. For basic information about
the MySQL statements to accomplish these tasks, see Section 13.1.8, “ALTER TABLE Syntax”. For more
detailed descriptions and examples, see Section 22.3, “Partition Management”.

• PARTITION BY

If used, a partition_options clause begins with PARTITION BY. This clause contains the function
that is used to determine the partition; the function returns an integer value ranging from 1 to num,
where num is the number of partitions. (The maximum number of user-defined partitions which a table
may contain is 1024; the number of subpartitions—discussed later in this section—is included in this
maximum.)

    Note
    The expression (expr) used in a PARTITION BY clause cannot refer to any
    columns not in the table being created; such references are specifically not
    permitted and cause the statement to fail with an error. (Bug #29444)

• HASH(expr)

Hashes one or more columns to create a key for placing and locating rows. expr is an expression
using one or more table columns. This can be any valid MySQL expression (including MySQL functions)
that yields a single integer value. For example, these are both valid CREATE TABLE statements using
PARTITION BY HASH:
```
CREATE TABLE t1 (col1 INT, col2 CHAR(5))
    PARTITION BY HASH(col1);

CREATE TABLE t1 (col1 INT, col2 CHAR(5), col3 DATETIME)
    PARTITION BY HASH ( YEAR(col3) );
```

You may not use either VALUES LESS THAN or VALUES IN clauses with PARTITION BY HASH.

PARTITION BY HASH uses the remainder of expr divided by the number of partitions (that is, the
modulus). For examples and additional information, see Section 22.2.4, “HASH Partitioning”.

The LINEAR keyword entails a somewhat different algorithm. In this case, the number of the partition in
which a row is stored is calculated as the result of one or more logical AND operations. For discussion
and examples of linear hashing, see Section 22.2.4.1, “LINEAR HASH Partitioning”.

• KEY(column_list)

This is similar to HASH, except that MySQL supplies the hashing function so as to guarantee an even
data distribution. The column_list argument is simply a list of 1 or more table columns (maximum:
16). This example shows a simple table partitioned by key, with 4 partitions:
```
CREATE TABLE tk (col1 INT, col2 CHAR(5), col3 DATE)
PARTITION BY KEY(col3)
PARTITIONS 4;
```

For tables that are partitioned by key, you can employ linear partitioning by using the LINEAR keyword.
This has the same effect as with tables that are partitioned by HASH. That is, the partition number is
found using the & operator rather than the modulus (see Section 22.2.4.1, “LINEAR HASH Partitioning”,
and Section 22.2.5, “KEY Partitioning”, for details). This example uses linear partitioning by key to
distribute data between 5 partitions:
```
CREATE TABLE tk (col1 INT, col2 CHAR(5), col3 DATE)
PARTITION BY LINEAR KEY(col3)
PARTITIONS 5;
```

The ALGORITHM={1|2} option is supported with [SUB]PARTITION BY [LINEAR] KEY.
ALGORITHM=1 causes the server to use the same key-hashing functions as MySQL 5.1; ALGORITHM=2
means that the server employs the key-hashing functions implemented and used by default for new KEY
partitioned tables in MySQL 5.5 and later. (Partitioned tables created with the key-hashing functions
employed in MySQL 5.5 and later cannot be used by a MySQL 5.1 server.) Not specifying the option
has the same effect as using ALGORITHM=2. This option is intended for use chiefly when upgrading or
downgrading [LINEAR] KEY partitioned tables between MySQL 5.1 and later MySQL versions, or for
creating tables partitioned by KEY or LINEAR KEY on a MySQL 5.5 or later server which can be used on
a MySQL 5.1 server. For more information, see Section 13.1.8.1, “ALTER TABLE Partition Operations”.

mysqldump in MySQL 5.7 (and later) writes this option encased in versioned comments, like this:
```
CREATE TABLE t1 (a INT)
/*!50100 PARTITION BY KEY */ /*!50611 ALGORITHM = 1 */ /*!50100 ()
PARTITIONS 3 */
```

This causes MySQL 5.6.10 and earlier servers to ignore the option, which would otherwise cause a
syntax error in those versions. If you plan to load a dump made on a MySQL 5.7 server where you use
tables that are partitioned or subpartitioned by KEY into a MySQL 5.6 server previous to version 5.6.11,
be sure to consult Changes Affecting Upgrades to MySQL 5.6, before proceeding. (The information
found there also applies if you are loading a dump containing KEY partitioned or subpartitioned tables
made from a MySQL 5.7—actually 5.6.11 or later—server into a MySQL 5.5.30 or earlier server.)

Also in MySQL 5.6.11 and later, ALGORITHM=1 is shown when necessary in the output of SHOW
CREATE TABLE using versioned comments in the same manner as mysqldump. ALGORITHM=2 is
always omitted from SHOW CREATE TABLE output, even if this option was specified when creating the
original table.

You may not use either VALUES LESS THAN or VALUES IN clauses with PARTITION BY KEY.

• RANGE(expr)

In this case, expr shows a range of values using a set of VALUES LESS THAN operators. When using
range partitioning, you must define at least one partition using VALUES LESS THAN. You cannot use
VALUES IN with range partitioning.

    Note
    For tables partitioned by RANGE, VALUES LESS THAN must be used with either
    an integer literal value or an expression that evaluates to a single integer value.
    In MySQL 8.0, you can overcome this limitation in a table that is defined using
    PARTITION BY RANGE COLUMNS, as described later in this section.

Suppose that you have a table that you wish to partition on a column containing year values, according
to the following scheme.

    Partition Number: Years Range:
    0 1990 and earlier
    1 1991 to 1994
    2 1995 to 1998
    3 1999 to 2002
    4 2003 to 2005
    5 2006 and later

A table implementing such a partitioning scheme can be realized by the CREATE TABLE statement
shown here:
```
CREATE TABLE t1 (
year_col INT,
some_data INT
)
PARTITION BY RANGE (year_col) (
PARTITION p0 VALUES LESS THAN (1991),
PARTITION p1 VALUES LESS THAN (1995),
PARTITION p2 VALUES LESS THAN (1999),
PARTITION p3 VALUES LESS THAN (2002),
PARTITION p4 VALUES LESS THAN (2006),
PARTITION p5 VALUES LESS THAN MAXVALUE
);
```

PARTITION ... VALUES LESS THAN ... statements work in a consecutive fashion. VALUES LESS
THAN MAXVALUE works to specify “leftover” values that are greater than the maximum value otherwise
specified.

VALUES LESS THAN clauses work sequentially in a manner similar to that of the case portions of a
switch ... case block (as found in many programming languages such as C, Java, and PHP). That
is, the clauses must be arranged in such a way that the upper limit specified in each successive VALUES
LESS THAN is greater than that of the previous one, with the one referencing MAXVALUE coming last of
all in the list.

• RANGE COLUMNS(column_list)

This variant on RANGE facilitates partition pruning for queries using range conditions on multiple columns
(that is, having conditions such as `WHERE a = 1 AND b < 10 or WHERE a = 1 AND b = 10
AND c < 10`). It enables you to specify value ranges in multiple columns by using a list of columns
in the COLUMNS clause and a set of column values in each PARTITION ... VALUES LESS THAN
(value_list) partition definition clause. (In the simplest case, this set consists of a single column.)
The maximum number of columns that can be referenced in the column_list and value_list is 16.

The column_list used in the COLUMNS clause may contain only names of columns; each column in
the list must be one of the following MySQL data types: the integer types; the string types; and time or
date column types. Columns using BLOB, TEXT, SET, ENUM, BIT, or spatial data types are not permitted;
columns that use floating-point number types are also not permitted. You also may not use functions or
arithmetic expressions in the COLUMNS clause.

The VALUES LESS THAN clause used in a partition definition must specify a literal value for each
column that appears in the COLUMNS() clause; that is, the list of values used for each VALUES LESS
THAN clause must contain the same number of values as there are columns listed in the COLUMNS
clause. An attempt to use more or fewer values in a VALUES LESS THAN clause than there are in the
COLUMNS clause causes the statement to fail with the error Inconsistency in usage of column
lists for partitioning.... You cannot use NULL for any value appearing in VALUES LESS
THAN. It is possible to use MAXVALUE more than once for a given column other than the first, as shown in
this example:
```
CREATE TABLE rc (
a INT NOT NULL,
b INT NOT NULL
)
PARTITION BY RANGE COLUMNS(a,b) (
PARTITION p0 VALUES LESS THAN (10,5),
PARTITION p1 VALUES LESS THAN (20,10),
PARTITION p2 VALUES LESS THAN (50,MAXVALUE),
PARTITION p3 VALUES LESS THAN (65,MAXVALUE),
PARTITION p4 VALUES LESS THAN (MAXVALUE,MAXVALUE)
);
```

Each value used in a VALUES LESS THAN value list must match the type of the corresponding column
exactly; no conversion is made. For example, you cannot use the string '1' for a value that matches a
column that uses an integer type (you must use the numeral 1 instead), nor can you use the numeral 1
for a value that matches a column that uses a string type (in such a case, you must use a quoted string:
'1').

For more information, see Section 22.2.1, “RANGE Partitioning”, and Section 22.4, “Partition Pruning”.

• LIST(expr)

This is useful when assigning partitions based on a table column with a restricted set of possible values,
such as a state or country code. In such a case, all rows pertaining to a certain state or country can be
assigned to a single partition, or a partition can be reserved for a certain set of states or countries. It
is similar to RANGE, except that only VALUES IN may be used to specify permissible values for each
partition.

VALUES IN is used with a list of values to be matched. For instance, you could create a partitioning
scheme such as the following:
```
CREATE TABLE client_firms (
id INT,
name VARCHAR(35)
)
PARTITION BY LIST (id) (
PARTITION r0 VALUES IN (1, 5, 9, 13, 17, 21),
PARTITION r1 VALUES IN (2, 6, 10, 14, 18, 22),
PARTITION r2 VALUES IN (3, 7, 11, 15, 19, 23),
PARTITION r3 VALUES IN (4, 8, 12, 16, 20, 24)
);
```
When using list partitioning, you must define at least one partition using VALUES IN. You cannot use
VALUES LESS THAN with PARTITION BY LIST.

    Note
    For tables partitioned by LIST, the value list used with VALUES IN must consist
    of integer values only. In MySQL 8.0, you can overcome this limitation using
    partitioning by LIST COLUMNS, which is described later in this section.

• LIST COLUMNS(column_list)

This variant on LIST facilitates partition pruning for queries using comparison conditions on multiple
columns (that is, having conditions such as WHERE a = 5 AND b = 5 or WHERE a = 1 AND b =
10 AND c = 5). It enables you to specify values in multiple columns by using a list of columns in the
COLUMNS clause and a set of column values in each PARTITION ... VALUES IN (value_list)
partition definition clause.

The rules governing regarding data types for the column list used in LIST COLUMNS(column_list)
and the value list used in VALUES IN(value_list) are the same as those for the column list used
in RANGE COLUMNS(column_list) and the value list used in VALUES LESS THAN(value_list),
respectively, except that in the VALUES IN clause, MAXVALUE is not permitted, and you may use NULL.

There is one important difference between the list of values used for VALUES IN with PARTITION
BY LIST COLUMNS as opposed to when it is used with PARTITION BY LIST. When used with
PARTITION BY LIST COLUMNS, each element in the VALUES IN clause must be a set of column
values; the number of values in each set must be the same as the number of columns used in the
COLUMNS clause, and the data types of these values must match those of the columns (and occur in the
same order). In the simplest case, the set consists of a single column. The maximum number of columns
that can be used in the column_list and in the elements making up the value_list is 16.

The table defined by the following CREATE TABLE statement provides an example of a table using LIST
COLUMNS partitioning:
```
CREATE TABLE lc (
a INT NULL,
b INT NULL
)
PARTITION BY LIST COLUMNS(a,b) (
PARTITION p0 VALUES IN( (0,0), (NULL,NULL) ),
PARTITION p1 VALUES IN( (0,1), (0,2), (0,3), (1,1), (1,2) ),
PARTITION p2 VALUES IN( (1,0), (2,0), (2,1), (3,0), (3,1) ),
PARTITION p3 VALUES IN( (1,3), (2,2), (2,3), (3,2), (3,3) )
);
```

• PARTITIONS num

The number of partitions may optionally be specified with a PARTITIONS num clause, where num is the
number of partitions. If both this clause and any PARTITION clauses are used, num must be equal to the
total number of any partitions that are declared using PARTITION clauses.

    Note
    Whether or not you use a PARTITIONS clause in creating a table that is
    partitioned by RANGE or LIST, you must still include at least one PARTITION
    VALUES clause in the table definition (see below).

• SUBPARTITION BY

A partition may optionally be divided into a number of subpartitions. This can be indicated by using the
optional SUBPARTITION BY clause. Subpartitioning may be done by HASH or KEY. Either of these may
be LINEAR. These work in the same way as previously described for the equivalent partitioning types. (It
is not possible to subpartition by LIST or RANGE.)

The number of subpartitions can be indicated using the SUBPARTITIONS keyword followed by an
integer value.

• Rigorous checking of the value used in PARTITIONS or SUBPARTITIONS clauses is applied and this
value must adhere to the following rules:
  • The value must be a positive, nonzero integer.
  • No leading zeros are permitted.
  • The value must be an integer literal, and cannot not be an expression. For example, PARTITIONS
  0.2E+01 is not permitted, even though 0.2E+01 evaluates to 2. (Bug #15890)

• partition_definition

  Each partition may be individually defined using a partition_definition clause. The individual
  parts making up this clause are as follows:
  • PARTITION partition_name
  Specifies a logical name for the partition.
  • VALUES
  For range partitioning, each partition must include a VALUES LESS THAN clause; for list partitioning,
  you must specify a VALUES IN clause for each partition. This is used to determine which rows are
  to be stored in this partition. See the discussions of partitioning types in Chapter 22, Partitioning, for
  syntax examples.
  • [STORAGE] ENGINE
  MySQL accepts a [STORAGE] ENGINE option for both PARTITION and SUBPARTITION. Currently,
  the only way in which this option can be used is to set all partitions or all subpartitions to the same
  storage engine, and an attempt to set different storage engines for partitions or subpartitions in the
  same table will give rise to the error ERROR 1469 (HY000): The mix of handlers in the
  partitions is not permitted in this version of MySQL.
  • COMMENT
  An optional COMMENT clause may be used to specify a string that describes the partition. Example:
  COMMENT = 'Data for the years previous to 1999'
  The maximum length for a partition comment is 1024 characters.

• DATA DIRECTORY and INDEX DIRECTORY

  DATA DIRECTORY and INDEX DIRECTORY may be used to indicate the directory where, respectively,
  the data and indexes for this partition are to be stored. Both the data_dir and the index_dir must
  be absolute system path names.
  You must have the FILE privilege to use the DATA DIRECTORY or INDEX DIRECTORY partition
  option.
  Example:
  ```
  CREATE TABLE th (id INT, name VARCHAR(30), adate DATE)
  PARTITION BY LIST(YEAR(adate))
  (
  PARTITION p1999 VALUES IN (1995, 1999, 2003)
  DATA DIRECTORY = '/var/appdata/95/data'
  INDEX DIRECTORY = '/var/appdata/95/idx',
  PARTITION p2000 VALUES IN (1996, 2000, 2004)
  DATA DIRECTORY = '/var/appdata/96/data'
  INDEX DIRECTORY = '/var/appdata/96/idx',
  PARTITION p2001 VALUES IN (1997, 2001, 2005)
  DATA DIRECTORY = '/var/appdata/97/data'
  INDEX DIRECTORY = '/var/appdata/97/idx',
  PARTITION p2002 VALUES IN (1998, 2002, 2006)
  DATA DIRECTORY = '/var/appdata/98/data'
  INDEX DIRECTORY = '/var/appdata/98/idx'
  );
  ```
  DATA DIRECTORY and INDEX DIRECTORY behave in the same way as in the CREATE TABLE
  statement's table_option clause as used for MyISAM tables.

  One data directory and one index directory may be specified per partition. If left unspecified, the data
  and indexes are stored by default in the table's database directory.

  The DATA DIRECTORY and INDEX DIRECTORY options are ignored for creating partitioned tables if
  NO_DIR_IN_CREATE is in effect.

• MAX_ROWS and MIN_ROWS

May be used to specify, respectively, the maximum and minimum number of rows to be stored in
the partition. The values for max_number_of_rows and min_number_of_rows must be positive
integers. As with the table-level options with the same names, these act only as “suggestions” to the
server and are not hard limits.

• TABLESPACE

May be used to designate an InnoDB file-per-table tablespace for the partition. All partitions must
belong to the same storage engine.

Placing InnoDB table partitions in shared InnoDB tablespaces is not supported. Shared tablespaces
include the InnoDB system tablespace and general tablespaces.

• subpartition_definition

The partition definition may optionally contain one or more subpartition_definition clauses.
Each of these consists at a minimum of the SUBPARTITION name, where name is an identifier for the
subpartition. Except for the replacement of the PARTITION keyword with SUBPARTITION, the syntax for
a subpartition definition is identical to that for a partition definition.

Subpartitioning must be done by HASH or KEY, and can be done only on RANGE or LIST partitions. See
Section 22.2.6, “Subpartitioning”.

### Partitioning by Generated Columns

Partitioning by generated columns is permitted. For example:
```
CREATE TABLE t1 (
s1 INT,
s2 INT AS (EXP(s1)) STORED
)
PARTITION BY LIST (s2) (
PARTITION p1 VALUES IN (1)
);
```
Partitioning sees a generated column as a regular column, which enables workarounds for limitations on
functions that are not permitted for partitioning (see Section 22.6.3, “Partitioning Limitations Relating to
Functions”). The preceding example demonstrates this technique: EXP() cannot be used directly in the
PARTITION BY clause, but a generated column defined using EXP() is permitted.

### 13.1.18.1 CREATE TABLE Statement Retention

The original CREATE TABLE statement, including all specifications and table options are stored by MySQL
when the table is created. The information is retained so that if you change storage engines, collations or
other settings using an ALTER TABLE statement, the original table options specified are retained. This
enables you to change between InnoDB and MyISAM table types even though the row formats supported
by the two engines are different.

Because the text of the original statement is retained, but due to the way that certain values and options
may be silently reconfigured, the active table definition (accessible through DESCRIBE or with SHOW
TABLE STATUS) and the table creation string (accessible through SHOW CREATE TABLE) may report
different values.

For InnoDB tables, SHOW CREATE TABLE and the Create_options column reported by SHOW TABLE
STATUS show the actual ROW_FORMAT and KEY_BLOCK_SIZE attributes used by the table. In previous
MySQL releases, the originally specified values for these attributes were reported.

### 13.1.18.2 Files Created by CREATE TABLE

For an InnoDB table created in a file-per-table tablespace or general tablespace, table data and
associated indexes are stored in an ibd file in the database directory. When an InnoDB table is created
in the system tablespace, table data and indexes are stored in the ibdata* files that represent the system
tablespace. The innodb_file_per_table option controls whether tables are created in file-per-
table tablespaces or the system tablespace, by default. The TABLESPACE option can be used to place
a table in a file-per-table tablespace, general tablespace, or the system tablespace, regardless of the
innodb_file_per_table setting.

For MyISAM tables, the storage engine creates data and index files. Thus, for each MyISAM table
tbl_name, there are two disk files.

    File                Purpose
    tbl_name.MYD        Data file
    tbl_name.MYI        Index file

Chapter 16, Alternative Storage Engines, describes what files each storage engine creates to represent
tables. If a table name contains special characters, the names for the table files contain encoded versions
of those characters as described in Section 9.2.3, “Mapping of Identifiers to File Names”.

### 13.1.18.3 CREATE TEMPORARY TABLE Syntax

### 13.1.18.4 CREATE TABLE ... LIKE Syntax

### 13.1.18.5 CREATE TABLE ... SELECT Syntax

### 13.1.18.6 Using FOREIGN KEY Constraints

### 13.1.18.7 Silent Column Specification Changes

### 13.1.18.8 CREATE TABLE and Generated Columns

### 13.1.18.9 Secondary Indexes and Generated Columns

## 13.1.19 CREATE TABLESPACE Syntax

```
CREATE TABLESPACE tablespace_name
    ADD DATAFILE 'file_name'
    [FILE_BLOCK_SIZE = value]
        [ENGINE [=] engine_name]
```

This statement is used to create an InnoDB tablespace. An InnoDB tablespace created using CREATE
TABLESPACE is referred to as general tablespace.

A general tablespace is a shared tablespace, similar to the system tablespace. It can hold multiple tables,
and supports all table row formats. General tablespaces can also be created in a location relative to or
independent of the MySQL data directory.

After creating an InnoDB general tablespace, you can use CREATE TABLE tbl_name ...
TABLESPACE [=] tablespace_name or ALTER TABLE tbl_name TABLESPACE [=]
tablespace_name to add tables to the tablespace.

For more information, see Section 15.7.10, “InnoDB General Tablespaces”.

    Note
    CREATE TABLESPACE is supported with InnoDB. In earlier releases, CREATE
    TABLESPACE only supported NDB, which is the MySQL NDB Cluster storage
    engine.

* ADD DATAFILE: Defines the name of the tablespace data file. A data file must be specified with the
  CREATE TABLESPACE statement, and the data file name must have a .ibd extension. An InnoDB
  general tablespace only supports a single data file.
  
  To place the data file in a location outside of the MySQL data directory (DATADIR), include an absolute
  directory path or a path relative to the MySQL data directory. If you do not specify a path, the general
  tablespace is created in the MySQL data directory.
  
  To avoid conflicts with implicitly created file-per-table tablespaces, creating a general tablespace in a
  subdirectory under the MySQL data directory is not supported. Also, when creating a general tablespace
  outside of the MySQL data directory, the directory must exist and must be known to InnoDB prior
  to creating the tablespace. To make an unknown directory known to InnoDB, add the directory to
  the innodb_directories argument value. innodb_directories is a read-only startup option.
  Configuring it requires restarting the server.
  
  The file_name, including the path (optional), must be quoted with single or double quotations marks.
  File names (not counting the “.ibd” extension) and directory names must be at least one byte in length.
  Zero length file names and directory names are not supported.

* FILE_BLOCK_SIZE: Defines the block size of the tablespace data file. If you do not specify this option,
  FILE_BLOCK_SIZE defaults to innodb_page_size. The FILE_BLOCK_SIZE setting is only required
  if you will use the tablespace to store compressed InnoDB tables (ROW_FORMAT=COMPRESSED). In this
  case, you must define the tablespace FILE_BLOCK_SIZE when creating the tablespace.
  
  If FILE_BLOCK_SIZE is equal innodb_page_size, the tablespace can only contain tables with
  an uncompressed row format (COMPACT, REDUNDANT, and DYNAMIC row formats). Tables with a
  COMPRESSED row format have a different physical page size than uncompressed tables. Therefore,
  compressed tables cannot coexist in the same tablespace as uncompressed tables.
  
  For a general tablespace to contain compressed tables, FILE_BLOCK_SIZE must be specified, and the
  FILE_BLOCK_SIZE value must be a valid compressed page size in relation to the innodb_page_size
  value. Also, the physical page size of the compressed table (KEY_BLOCK_SIZE) must be equal to
  FILE_BLOCK_SIZE/1024. For example, if innodb_page_size=16K, and FILE_BLOCK_SIZE=8K,
  the KEY_BLOCK_SIZE of the table must be 8. For more information, see Section 15.7.10, “InnoDB
  General Tablespaces”.

* ENGINE: Defines the storage engine which uses the tablespace, where engine_name is the name of
  the storage engine. Currently, only the InnoDB storage engine is supported. ENGINE = InnoDB must
  be defined as part of the CREATE TABLESPACE statement or InnoDB must be defined as the default
  storage engine (default_storage_engine=InnoDB).

### Notes

• tablespace_name is a case-sensitive identifier for the tablespace. It may be quoted or unquoted. The
forward slash character (“/”) is not permitted. Names beginning with innodb_ are either not permitted or
are reserved for special use.
• Creation of temporary general tablespaces is not supported.
• General tablespaces do not support temporary tables.
• The TABLESPACE option may be used with CREATE TABLE or ALTER TABLE to assign an InnoDB
table partition or subpartition to a file-per-table tablespace. All partitions must belong to the same storage
engine. Assigning table partitions to shared InnoDB tablespaces is not supported. Shared tablespaces
include the InnoDB system tablespace and general tablespaces.
• General tablespaces support the addition of tables of any row format using CREATE TABLE ...
TABLESPACE. innodb_file_per_table does not need to be enabled.
• innodb_strict_mode is not applicable to general tablespaces. Tablespace management rules
are strictly enforced independently of innodb_strict_mode. If CREATE TABLESPACE parameters
are incorrect or incompatible, the operation fails regardless of the innodb_strict_mode setting.
When a table is added to a general tablespace using CREATE TABLE ... TABLESPACE or ALTER
TABLE ... TABLESPACE, innodb_strict_mode is ignored but the statement is evaluated as if
innodb_strict_mode is enabled.
• Use DROP TABLESPACE to remove a general tablespace. All tables must be dropped from a general
tablespace using DROP TABLE prior to dropping the tablespace.
• All parts of a table added to a general tablespace reside in the general tablespace, including indexes and
BLOB pages.
• Similar to the system tablespace, truncating or dropping tables stored in a general tablespace creates
free space internally in the general tablespace .ibd data file which can only be used for new InnoDB
data. Space is not released back to the operating system as it is for file-per-table tablespaces.
• A general tablespace is not associated with any database or schema.
• ALTER TABLE ... DISCARD TABLESPACE and ALTER TABLE ...IMPORT TABLESPACE are not
supported for tables that belong to a general tablespace.
• The server uses tablespace-level metadata locking for DDL that references general tablespaces.
By comparison, the server uses table-level metadata locking for DDL that references file-per-table
tablespaces.
• A generated or existing tablespace cannot be changed to a general tablespace.
• There is no conflict between general tablespace names and file-per-table tablespace names. The “/”
character, which is present in file-per-table tablespace names, is not permitted in general tablespace
names.

### Examples

This example demonstrates creating a general tablespace and adding three uncompressed tables of
different row formats.
```
mysql> CREATE TABLESPACE `ts1` ADD DATAFILE 'ts1.ibd' Engine=InnoDB;
Query OK, 0 rows affected (0.01 sec)
mysql> CREATE TABLE t1 (c1 INT PRIMARY KEY) TABLESPACE ts1 ROW_FORMAT=REDUNDANT;
Query OK, 0 rows affected (0.00 sec)
mysql> CREATE TABLE t2 (c1 INT PRIMARY KEY) TABLESPACE ts1 ROW_FORMAT=COMPACT;
Query OK, 0 rows affected (0.00 sec)
mysql> CREATE TABLE t3 (c1 INT PRIMARY KEY) TABLESPACE ts1 ROW_FORMAT=DYNAMIC;
Query OK, 0 rows affected (0.00 sec)
```

This example demonstrates creating a general tablespace and adding a compressed table. The example
assumes a default innodb_page_size of 16K. The FILE_BLOCK_SIZE of 8192 requires that the
compressed table have a KEY_BLOCK_SIZE of 8.
```
mysql> CREATE TABLESPACE `ts2` ADD DATAFILE 'ts2.ibd' FILE_BLOCK_SIZE = 8192 Engine=InnoDB;
Query OK, 0 rows affected (0.01 sec)
mysql> CREATE TABLE t4 (c1 INT PRIMARY KEY) TABLESPACE ts2 ROW_FORMAT=COMPRESSED KEY_BLOCK_SIZE=8;
Query OK, 0 rows affected (0.00 sec)
```

## 13.1.20 CREATE TRIGGER Syntax

```
CREATE
[DEFINER = { user | CURRENT_USER }]
TRIGGER trigger_name
trigger_time trigger_event
ON tbl_name FOR EACH ROW
[trigger_order]
trigger_body
trigger_time: { BEFORE | AFTER }
trigger_event: { INSERT | UPDATE | DELETE }
trigger_order: { FOLLOWS | PRECEDES } other_trigger_name
```

This statement creates a new trigger. A trigger is a named database object that is associated with a table,
and that activates when a particular event occurs for the table. The trigger becomes associated with the
table named tbl_name, which must refer to a permanent table. You cannot associate a trigger with a
TEMPORARY table or a view.

Trigger names exist in the schema namespace, meaning that all triggers must have unique names within a
schema. Triggers in different schemas can have the same name.

This section describes CREATE TRIGGER syntax. For additional discussion, see Section 23.3.1, “Trigger
Syntax and Examples”.

CREATE TRIGGER requires the TRIGGER privilege for the table associated with the trigger. The statement
might also require the SET_USER_ID or SUPER privilege, depending on the DEFINER value, as described
later in this section. If binary logging is enabled, CREATE TRIGGER might require the SUPER privilege, as
described in Section 23.7, “Binary Logging of Stored Programs”.

The DEFINER clause determines the security context to be used when checking access privileges at
trigger activation time, as described later in this section.

trigger_time is the trigger action time. It can be BEFORE or AFTER to indicate that the trigger activates
before or after each row to be modified.

Basic column value checks occur prior to trigger activation, so you cannot use BEFORE triggers to convert
values inappropriate for the column type to valid values.

trigger_event indicates the kind of operation that activates the trigger. These trigger_event values
are permitted:
• INSERT: The trigger activates whenever a new row is inserted into the table; for example, through
INSERT, LOAD DATA, and REPLACE statements.
• UPDATE: The trigger activates whenever a row is modified; for example, through UPDATE statements.
• DELETE: The trigger activates whenever a row is deleted from the table; for example, through DELETE
and REPLACE statements. DROP TABLE and TRUNCATE TABLE statements on the table do not activate
this trigger, because they do not use DELETE. Dropping a partition does not activate DELETE triggers,
either.

The trigger_event does not represent a literal type of SQL statement that activates the trigger so much
as it represents a type of table operation. For example, an INSERT trigger activates not only for INSERT
statements but also LOAD DATA statements because both statements insert rows into a table.

A potentially confusing example of this is the INSERT INTO ... ON DUPLICATE KEY UPDATE ...
syntax: a BEFORE INSERT trigger activates for every row, followed by either an AFTER INSERT trigger or
both the BEFORE UPDATE and AFTER UPDATE triggers, depending on whether there was a duplicate key
for the row.

    Note
    Cascaded foreign key actions do not activate triggers.

It is possible to define multiple triggers for a given table that have the same trigger event and action time.
For example, you can have two BEFORE UPDATE triggers for a table. By default, triggers that have the
same trigger event and action time activate in the order they were created. To affect trigger order, specify
a trigger_order clause that indicates FOLLOWS or PRECEDES and the name of an existing trigger that
also has the same trigger event and action time. With FOLLOWS, the new trigger activates after the existing
trigger. With PRECEDES, the new trigger activates before the existing trigger.

trigger_body is the statement to execute when the trigger activates. To execute multiple statements,
use the BEGIN ... END compound statement construct. This also enables you to use the same
statements that are permitted within stored routines. See Section 13.6.1, “BEGIN ... END Compound-
Statement Syntax”. Some statements are not permitted in triggers; see Section C.1, “Restrictions on
Stored Programs”.

Within the trigger body, you can refer to columns in the subject table (the table associated with the trigger)
by using the aliases OLD and NEW. OLD.col_name refers to a column of an existing row before it is
updated or deleted. NEW.col_name refers to the column of a new row to be inserted or an existing row
after it is updated.

Triggers cannot use NEW.col_name or use OLD.col_name to refer to generated columns. For
information about generated columns, see Section 13.1.18.8, “CREATE TABLE and Generated Columns”.
MySQL stores the sql_mode system variable setting in effect when a trigger is created, and always
executes the trigger body with this setting in force, regardless of the current server SQL mode when the
trigger begins executing.

The DEFINER clause specifies the MySQL account to be used when checking access privileges
at trigger activation time. If a user value is given, it should be a MySQL account specified as
'user_name'@'host_name', CURRENT_USER, or CURRENT_USER(). The default DEFINER value is
the user who executes the CREATE TRIGGER statement. This is the same as specifying `DEFINER = CURRENT_USER` explicitly.

If you specify the DEFINER clause, these rules determine the valid DEFINER user values:

• If you do not have the SET_USER_ID or SUPER privilege, the only permitted user value is your own
account, either specified literally or by using CURRENT_USER. You cannot set the definer to some other
account.
• If you have the SET_USER_ID or SUPER privilege, you can specify any syntactically valid account name.
If the account does not exist, a warning is generated.
• Although it is possible to create a trigger with a nonexistent DEFINER account, it is not a good idea for
such triggers to be activated until the account actually does exist. Otherwise, the behavior with respect to
privilege checking is undefined.

MySQL takes the DEFINER user into account when checking trigger privileges as follows:
• At CREATE TRIGGER time, the user who issues the statement must have the TRIGGER privilege.
• At trigger activation time, privileges are checked against the DEFINER user. This user must have these
privileges:
    • The TRIGGER privilege for the subject table.
    • The SELECT privilege for the subject table if references to table columns occur using OLD.col_name
    or NEW.col_name in the trigger body.
    • The UPDATE privilege for the subject table if table columns are targets of SET NEW.col_name =
    value assignments in the trigger body.
    • Whatever other privileges normally are required for the statements executed by the trigger.

For more information about trigger security, see Section 23.6, “Access Control for Stored Programs and
Views”.

Within a trigger body, the CURRENT_USER() function returns the account used to check privileges at
trigger activation time. This is the DEFINER user, not the user whose actions caused the trigger to be
activated. For information about user auditing within triggers, see Section 6.3.13, “SQL-Based MySQL
Account Activity Auditing”.

If you use LOCK TABLES to lock a table that has triggers, the tables used within the trigger are also locked,
as described in Section 13.3.6.2, “LOCK TABLES and Triggers”.

For additional discussion of trigger use, see Section 23.3.1, “Trigger Syntax and Examples”.

## 13.1.21 CREATE VIEW Syntax

```
CREATE
[OR REPLACE]
[ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
[DEFINER = { user | CURRENT_USER }]
[SQL SECURITY { DEFINER | INVOKER }]
VIEW view_name [(column_list)]
AS select_statement
[WITH [CASCADED | LOCAL] CHECK OPTION]
```

The CREATE VIEW statement creates a new view, or replaces an existing view if the OR REPLACE clause
is given. If the view does not exist, CREATE OR REPLACE VIEW is the same as CREATE VIEW. If the view
does exist, CREATE OR REPLACE VIEW replaces it.
For information about restrictions on view use, see Section C.5, “Restrictions on Views”.
The select_statement is a SELECT statement that provides the definition of the view. (Selecting from
the view selects, in effect, using the SELECT statement.) The select_statement can select from base
tables or other views.
The view definition is “frozen” at creation time and is not affected by subsequent changes to the definitions
of the underlying tables. For example, if a view is defined as SELECT * on a table, new columns added to
the table later do not become part of the view, and columns dropped from the table will result in an error
when selecting from the view.
The ALGORITHM clause affects how MySQL processes the view. The DEFINER and SQL SECURITY
clauses specify the security context to be used when checking access privileges at view invocation
time. The WITH CHECK OPTION clause can be given to constrain inserts or updates to rows in tables
referenced by the view. These clauses are described later in this section.
The CREATE VIEW statement requires the CREATE VIEW privilege for the view, and some privilege for
each column selected by the SELECT statement. For columns used elsewhere in the SELECT statement,
you must have the SELECT privilege. If the OR REPLACE clause is present, you must also have the DROP
privilege for the view. CREATE VIEW might also require the SET_USER_ID or SUPER privilege, depending
on the DEFINER value, as described later in this section.
When a view is referenced, privilege checking occurs as described later in this section.
A view belongs to a database. By default, a new view is created in the default database. To create the
view explicitly in a given database, use db_name.view_name syntax to qualify the view name with the
database name:
```
CREATE VIEW test.v AS SELECT * FROM t;
```

Unqualified table or view names in the SELECT statement are also interpreted with respect to the default
database. A view can refer to tables or views in other databases by qualifying the table or view name with
the appropriate database name.

Within a database, base tables and views share the same namespace, so a base table and a view cannot
have the same name.

Columns retrieved by the SELECT statement can be simple references to table columns, or expressions
that use functions, constant values, operators, and so forth.

A view must have unique column names with no duplicates, just like a base table. By default, the names
of the columns retrieved by the SELECT statement are used for the view column names. To define explicit
names for the view columns, specify the optional column_list clause as a list of comma-separated
identifiers. The number of names in column_list must be the same as the number of columns retrieved
by the SELECT statement.

A view can be created from many kinds of SELECT statements. It can refer to base tables or other views. It
can use joins, UNION, and subqueries. The SELECT need not even refer to any tables:
```
CREATE VIEW v_today (today) AS SELECT CURRENT_DATE;
```

The following example defines a view that selects two columns from another table as well as an expression
calculated from those columns:
```
mysql> CREATE TABLE t (qty INT, price INT);
mysql> INSERT INTO t VALUES(3, 50);
mysql> CREATE VIEW v AS SELECT qty, price, qty*price AS value FROM t;
mysql> SELECT * FROM v;
+------+-------+-------+
| qty | price | value |
+------+-------+-------+
| 3 | 50 | 150 |
+------+-------+-------+
```

A view definition is subject to the following restrictions:
• The SELECT statement cannot refer to system variables or user-defined variables.
• Within a stored program, the SELECT statement cannot refer to program parameters or local variables.
• The SELECT statement cannot refer to prepared statement parameters.
• Any table or view referred to in the definition must exist. If, after the view has been created, a table or
view that the definition refers to is dropped, use of the view results in an error. To check a view definition
for problems of this kind, use the CHECK TABLE statement.
• The definition cannot refer to a TEMPORARY table, and you cannot create a TEMPORARY view.
• You cannot associate a trigger with a view.
• Aliases for column names in the SELECT statement are checked against the maximum column length of
64 characters (not the maximum alias length of 256 characters).
ORDER BY is permitted in a view definition, but it is ignored if you select from a view using a statement that
has its own ORDER BY.

For other options or clauses in the definition, they are added to the options or clauses of the statement that
references the view, but the effect is undefined. For example, if a view definition includes a LIMIT clause,
and you select from the view using a statement that has its own LIMIT clause, it is undefined which limit
applies. This same principle applies to options such as ALL, DISTINCT, or SQL_SMALL_RESULT that
follow the SELECT keyword, and to clauses such as INTO, FOR UPDATE, FOR SHARE, LOCK IN SHARE
MODE, and PROCEDURE.

The results obtained from a view may be affected if you change the query processing environment by
changing system variables:
```
mysql> CREATE VIEW v (mycol) AS SELECT 'abc';
Query OK, 0 rows affected (0.01 sec)

mysql> SET sql_mode = '';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT "mycol" FROM v;
+-------+
| mycol |
+-------+
| mycol |
+-------+
1 row in set (0.01 sec)

mysql> SET sql_mode = 'ANSI_QUOTES';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT "mycol" FROM v;
+-------+
| mycol |
+-------+
| abc |
+-------+
1 row in set (0.00 sec)
```

The DEFINER and SQL SECURITY clauses determine which MySQL account to use when checking
access privileges for the view when a statement is executed that references the view. The valid SQL
SECURITY characteristic values are DEFINER (the default) and INVOKER. These indicate that the required
privileges must be held by the user who defined or invoked the view, respectively.

If a user value is given for the DEFINER clause, it should be a MySQL account specified as
'user_name'@'host_name', CURRENT_USER, or CURRENT_USER(). The default DEFINER value
is the user who executes the CREATE VIEW statement. This is the same as specifying DEFINER =
CURRENT_USER explicitly.

If the DEFINER clause is present, these rules determine the valid DEFINER user values:
• If you do not have the SET_USER_ID or SUPER privilege, the only valid user value is your own account,
either specified literally or by using CURRENT_USER. You cannot set the definer to some other account.
• If you have the SET_USER_ID or SUPER privilege, you can specify any syntactically valid account name.
If the account does not exist, a warning is generated.
• Although it is possible to create a view with a nonexistent DEFINER account, an error occurs when the
view is referenced if the SQL SECURITY value is DEFINER but the definer account does not exist.
For more information about view security, see Section 23.6, “Access Control for Stored Programs and
Views”.

Within a view definition, CURRENT_USER returns the view's DEFINER value by default. For views defined
with the SQL SECURITY INVOKER characteristic, CURRENT_USER returns the account for the view's
invoker. For information about user auditing within views, see Section 6.3.13, “SQL-Based MySQL Account
Activity Auditing”.

Within a stored routine that is defined with the SQL SECURITY DEFINER characteristic, CURRENT_USER
returns the routine's DEFINER value. This also affects a view defined within such a routine, if the view
definition contains a DEFINER value of CURRENT_USER.

MySQL checks view privileges like this:
• At view definition time, the view creator must have the privileges needed to use the top-level objects
accessed by the view. For example, if the view definition refers to table columns, the creator must have
some privilege for each column in the select list of the definition, and the SELECT privilege for each
column used elsewhere in the definition. If the definition refers to a stored function, only the privileges
needed to invoke the function can be checked. The privileges required at function invocation time can be
checked only as it executes: For different invocations, different execution paths within the function might
be taken.
• The user who references a view must have appropriate privileges to access it (SELECT to select from it,
INSERT to insert into it, and so forth.)
• When a view has been referenced, privileges for objects accessed by the view are checked against the
privileges held by the view DEFINER account or invoker, depending on whether the SQL SECURITY
characteristic is DEFINER or INVOKER, respectively.
• If reference to a view causes execution of a stored function, privilege checking for statements executed
within the function depend on whether the function SQL SECURITY characteristic is DEFINER or
INVOKER. If the security characteristic is DEFINER, the function runs with the privileges of the DEFINER
account. If the characteristic is INVOKER, the function runs with the privileges determined by the view's
SQL SECURITY characteristic.

Example: A view might depend on a stored function, and that function might invoke other stored routines.
For example, the following view invokes a stored function f():
```
CREATE VIEW v AS SELECT * FROM t WHERE t.id = f(t.name);
Suppose that f() contains a statement such as this:
IF name IS NULL then
CALL p1();
ELSE
CALL p2();
END IF;
```

The privileges required for executing statements within f() need to be checked when f() executes. This
might mean that privileges are needed for p1() or p2(), depending on the execution path within f().
Those privileges must be checked at runtime, and the user who must possess the privileges is determined
by the SQL SECURITY values of the view v and the function f().

The DEFINER and SQL SECURITY clauses for views are extensions to standard SQL. In standard SQL,
views are handled using the rules for SQL SECURITY DEFINER. The standard says that the definer of
the view, which is the same as the owner of the view's schema, gets applicable privileges on the view (for
example, SELECT) and may grant them. MySQL has no concept of a schema “owner”, so MySQL adds
a clause to identify the definer. The DEFINER clause is an extension where the intent is to have what the
standard has; that is, a permanent record of who defined the view. This is why the default DEFINER value
is the account of the view creator.

The optional ALGORITHM clause is a MySQL extension to standard SQL. It affects how MySQL processes
the view. ALGORITHM takes three values: MERGE, TEMPTABLE, or UNDEFINED. For more information, see
Section 23.5.2, “View Processing Algorithms”, as well as Section 8.2.2.3, “Optimizing Derived Tables, View
References, and Common Table Expressions”.

Some views are updatable. That is, you can use them in statements such as UPDATE, DELETE, or INSERT
to update the contents of the underlying table. For a view to be updatable, there must be a one-to-one
relationship between the rows in the view and the rows in the underlying table. There are also certain other
constructs that make a view nonupdatable.

A generated column in a view is considered updatable because it is possible to assign to it. However, if
such a column is updated explicitly, the only permitted value is DEFAULT. For information about generated
columns, see Section 13.1.18.8, “CREATE TABLE and Generated Columns”.

The WITH CHECK OPTION clause can be given for an updatable view to prevent inserts or updates to
rows except those for which the WHERE clause in the select_statement is true.

In a WITH CHECK OPTION clause for an updatable view, the LOCAL and CASCADED keywords determine
the scope of check testing when the view is defined in terms of another view. The LOCAL keyword restricts
the CHECK OPTION only to the view being defined. CASCADED causes the checks for underlying views to
be evaluated as well. When neither keyword is given, the default is CASCADED.

For more information about updatable views and the WITH CHECK OPTION clause, see Section 23.5.3,
“Updatable and Insertable Views”, and Section 23.5.4, “The View WITH CHECK OPTION Clause”.

## 13.1.22 DROP DATABASE Syntax

```
DROP {DATABASE | SCHEMA} [IF EXISTS] db_name
```

DROP DATABASE drops all tables in the database and deletes the database. Be very careful with this
statement! To use DROP DATABASE, you need the DROP privilege on the database. DROP SCHEMA is a
synonym for DROP DATABASE.

    Important
    When a database is dropped, privileges granted specifically for the database are
    not automatically dropped. They must be dropped manually. See Section 13.7.1.6,
    “GRANT Syntax”.

IF EXISTS is used to prevent an error from occurring if the database does not exist.

If the default database is dropped, the default database is unset (the DATABASE() function returns NULL).
If you use DROP DATABASE on a symbolically linked database, both the link and the original database are
deleted.

DROP DATABASE returns the number of tables that were removed.

The DROP DATABASE statement removes from the given database directory those files and directories that
MySQL itself may create during normal operation. This includes all files with the extensions shown in the
following list:
• .BAK
• .DAT
• .HSH
• .MRG
• .MYD
• .MYI
• .cfg
• .db
• .ibd
• .ndb

If other files or directories remain in the database directory after MySQL removes those just listed, the
database directory cannot be removed. In this case, you must remove any remaining files or directories
manually and issue the DROP DATABASE statement again.

Dropping a database does not remove any TEMPORARY tables that were created in that database.
TEMPORARY tables are automatically removed when the session that created them ends. See
Section 13.1.18.3, “CREATE TEMPORARY TABLE Syntax”.

You can also drop databases with mysqladmin. See Section 4.5.2, “mysqladmin — Client for
Administering a MySQL Server”.

## 13.1.23 DROP EVENT Syntax

```
DROP EVENT [IF EXISTS] event_name
```

This statement drops the event named event_name. The event immediately ceases being active, and is
deleted completely from the server.

If the event does not exist, the error ERROR 1517 (HY000): Unknown event 'event_name' results.
You can override this and cause the statement to generate a warning for nonexistent events instead using
IF EXISTS.

This statement requires the EVENT privilege for the schema to which the event to be dropped belongs.

## 13.1.24 DROP FUNCTION Syntax

The DROP FUNCTION statement is used to drop stored functions and user-defined functions (UDFs):

• For information about dropping stored functions, see Section 13.1.26, “DROP PROCEDURE and DROP
FUNCTION Syntax”.

• For information about dropping user-defined functions, see Section 13.7.4.2, “DROP FUNCTION
Syntax”.

## 13.1.25 DROP INDEX Syntax

```
DROP INDEX index_name ON tbl_name
[algorithm_option | lock_option] ...
algorithm_option:
ALGORITHM [=] {DEFAULT|INPLACE|COPY}
lock_option:
LOCK [=] {DEFAULT|NONE|SHARED|EXCLUSIVE}
```

DROP INDEX drops the index named index_name from the table tbl_name. This statement is mapped to
an ALTER TABLE statement to drop the index. See Section 13.1.8, “ALTER TABLE Syntax”.

To drop a primary key, the index name is always PRIMARY, which must be specified as a quoted identifier
because PRIMARY is a reserved word:
```
DROP INDEX `PRIMARY` ON t;
```

ALGORITHM and LOCK clauses may be given to influence the table copying method and level of
concurrency for reading and writing the table while its indexes are being modified. They have the same
meaning as for the ALTER TABLE statement. For more information, see Section 13.1.8, “ALTER TABLE
Syntax”

## 13.1.26 DROP PROCEDURE and DROP FUNCTION Syntax

```
DROP {PROCEDURE | FUNCTION} [IF EXISTS] sp_name
```

This statement is used to drop a stored procedure or function. That is, the specified routine is
removed from the server. You must have the ALTER ROUTINE privilege for the routine. (If the
automatic_sp_privileges system variable is enabled, that privilege and EXECUTE are granted
automatically to the routine creator when the routine is created and dropped from the creator when the
routine is dropped. See Section 23.2.2, “Stored Routines and MySQL Privileges”.)

The IF EXISTS clause is a MySQL extension. It prevents an error from occurring if the procedure or
function does not exist. A warning is produced that can be viewed with SHOW WARNINGS.

DROP FUNCTION is also used to drop user-defined functions (see Section 13.7.4.2, “DROP FUNCTION
Syntax”).


## 13.1.27 DROP SERVER Syntax

```
DROP SERVER [ IF EXISTS ] server_name
```

Drops the server definition for the server named server_name. The corresponding row in the
mysql.servers table is deleted. This statement requires the SUPER privilege.

Dropping a server for a table does not affect any FEDERATED tables that used this connection information
when they were created. See Section 13.1.16, “CREATE SERVER Syntax”.

DROP SERVER causes an implicit commit. See Section 13.3.3, “Statements That Cause an Implicit
Commit”.

DROP SERVER is not written to the binary log, regardless of the logging format that is in use.

## 13.1.28 DROP SPATIAL REFERENCE SYSTEM Syntax

```
DROP SPATIAL REFERENCE SYSTEM
    [IF EXISTS]
    srid

srid: 32-bit unsigned integer
```
This statement removes a spatial reference system (SRS) definition from the data dictionary. It requires the
SUPER privilege.

Example:
```
DROP SPATIAL REFERENCE SYSTEM 4120;
```

If no SRS definition with the SRID value exists, an error occurs unless IF EXISTS is specified. In that
case, a warning occurs rather than an error.

If the SRID value is used by some column in an existing table, an error occurs. For example:
```
mysql> DROP SPATIAL REFERENCE SYSTEM 4326;
ERROR 3716 (SR005): Can't modify SRID 4326. There is at
least one column depending on it.
```

To identify which column or columns use the SRID, use this query:
```
SELECT * FROM INFORMATION_SCHEMA.ST_GEOMETRY_COLUMNS WHERE SRS_ID=4326;
```

SRID values must be in the range of 32-bit unsigned integers, with these restrictions:
• SRID 0 is a valid SRID but cannot be used with DROP SPATIAL REFERENCE SYSTEM.
• If the value is in a reserved SRID range, a warning occurs. Reserved ranges are [0, 32767] (reserved by
EPSG), [60,000,000, 69,999,999] (reserved by EPSG), and [2,000,000,000, 2,147,483,647] (reserved by
MySQL). EPSG stands for the European Petroleum Survey Group.
• Users should not drop SRSs with SRIDs in the reserved ranges. If system-installed SRSs are dropped,
the SRS definitions may be recreated for MySQL upgrades.

## 13.1.29 DROP TABLE Syntax

```
DROP [TEMPORARY] TABLE [IF EXISTS]
    tbl_name [, tbl_name] ...
    [RESTRICT | CASCADE]
```

DROP TABLE removes one or more tables. You must have the DROP privilege for each table.

Be careful with this statement! It removes the table definition and all table data. For a partitioned table, it
permanently removes the table definition, all its partitions, and all data stored in those partitions. It also
removes partition definitions associated with the dropped table.

DROP TABLE causes an implicit commit, except when used with the TEMPORARY keyword. See
Section 13.3.3, “Statements That Cause an Implicit Commit”.

    Important
    When a table is dropped, privileges granted specifically for the table are not
    automatically dropped. They must be dropped manually. See Section 13.7.1.6,
    “GRANT Syntax”.

If any tables named in the argument list do not exist, the statement fails with an error indicating by name
which nonexisting tables it was unable to drop, and no changes are made.

Use IF EXISTS to prevent an error from occurring for tables that do not exist. Instead of an error, a
NOTE is generated for each nonexistent table; these notes can be displayed with SHOW WARNINGS. See
Section 13.7.6.40, “SHOW WARNINGS Syntax”.

IF EXISTS can also be useful for dropping tables in unusual circumstances under which there is an entry
in the data dictionary but no table managed by the storage engine. (For example, if an abnormal server exit
occurs after removal of the table from the storage engine but before removal of the data dictionary entry.)

The TEMPORARY keyword has the following effects:
• The statement drops only TEMPORARY tables.
• The statement does not cause an implicit commit.
• No access rights are checked. A TEMPORARY table is visible only with the session that created it, so no
check is necessary.

Using TEMPORARY is a good way to ensure that you do not accidentally drop a non-TEMPORARY table.
The RESTRICT and CASCADE keywords do nothing. They are permitted to make porting easier from other
database systems.

DROP TABLE is not supported with all innodb_force_recovery settings. See Section 15.20.2, “Forcing
InnoDB Recovery”.

## 13.1.30 DROP TABLESPACE Syntax

```
DROP TABLESPACE tablespace_name
    [ENGINE [=] engine_name]
```

This statement is used to drop an InnoDB general tablespace created using CREATE TABLESPACE
syntax. (see Section 13.1.19, “CREATE TABLESPACE Syntax”).

All tables must be dropped from the tablespace prior to a DROP TABLESPACE operation. If the tablespace
is not empty, DROP TABLESPACE returns an error.

tablespace_name is a case-sensitive identifier in MySQL.

ENGINE: Defines the storage engine that uses the tablespace, where engine_name is the name of the
storage engine. Currently, only the InnoDB storage engine is supported.

    Note
    The ENGINE clause is deprecated and will be removed in a future release. The
    tablespace storage engine is known by the data dictionary, making the ENGINE
    clause obsolete.

### Notes

• A general InnoDB tablespace is not deleted automatically when the last table in the tablespace is
dropped. The tablespace must be dropped explicitly using DROP TABLESPACE tablespace_name.
• A DROP DATABASE operation can drop tables that belong to a general tablespace but it cannot drop the
tablespace, even if the operation drops all tables that belong to the tablespace. The tablespace must be
dropped explicitly using DROP TABLESPACE tablespace_name.
• Similar to the system tablespace, truncating or dropping tables stored in a general tablespace creates
free space internally in the general tablespace .ibd data file which can only be used for new InnoDB
data. Space is not released back to the operating system as it is for file-per-table tablespaces.

### Example

This example demonstrates how to drop an InnoDB general tablespace. The general tablespace ts1 is
created with a single table. Before dropping the tablespace, the table must be dropped.

```
mysql> CREATE TABLESPACE `ts1` ADD DATAFILE 'ts1.ibd' Engine=InnoDB;
Query OK, 0 rows affected (0.01 sec)
mysql> CREATE TABLE t1 (c1 INT PRIMARY KEY) TABLESPACE ts10 Engine=InnoDB;
Query OK, 0 rows affected (0.02 sec)
mysql> DROP TABLE t1;
Query OK, 0 rows affected (0.01 sec)
mysql> DROP TABLESPACE ts1;
Query OK, 0 rows affected (0.01 sec)
```

## 13.1.31 DROP TRIGGER Syntax

```
DROP TRIGGER [IF EXISTS] [schema_name.]trigger_name
```

This statement drops a trigger. The schema (database) name is optional. If the schema is omitted, the
trigger is dropped from the default schema. DROP TRIGGER requires the TRIGGER privilege for the table
associated with the trigger.

Use IF EXISTS to prevent an error from occurring for a trigger that does not exist. A NOTE is generated
for a nonexistent trigger when using IF EXISTS. See Section 13.7.6.40, “SHOW WARNINGS Syntax”.

Triggers for a table are also dropped if you drop the table.

## 13.1.32 DROP VIEW Syntax

```
DROP VIEW [IF EXISTS]
view_name [, view_name] ...
[RESTRICT | CASCADE]
```

DROP VIEW removes one or more views. You must have the DROP privilege for each view.

If any views named in the argument list do not exist, the statement fails with an error indicating by name
which nonexisting views it was unable to drop, and no changes are made.

    Note
    In MySQL 5.7 and earlier, DROP VIEW returns an error if any views named in the
    argument list do not exist, but also drops all views in the list that do exist. Due to the
    change in behavior in MySQL 8.0, a partially completed DROP VIEW operation on a
    MySQL 5.7 master fails when replicated on a MySQL 8.0 slave. To avoid this failure
    scenario, use IF EXISTS syntax in DROP VIEW statements to prevent an error
    from occurring for views that do not exist. For more information, see Section 13.1.1,
    “Atomic Data Definition Statement Support”.

The IF EXISTS clause prevents an error from occurring for views that don't exist. When this clause
is given, a NOTE is generated for each nonexistent view. See Section 13.7.6.40, “SHOW WARNINGS
Syntax”.

RESTRICT and CASCADE, if given, are parsed and ignored.

## 13.1.33 RENAME TABLE Syntax

```
RENAME TABLE
    tbl_name TO new_tbl_name
    [, tbl_name2 TO new_tbl_name2] ...
```
RENAME TABLE renames one or more tables. You must have ALTER and DROP privileges for the original
table, and CREATE and INSERT privileges for the new table.

For example, to rename a table named old_table to to new_table, use this statement:
RENAME TABLE old_table TO new_table;

That statement is equivalent to the following ALTER TABLE statement:
```
ALTER TABLE old_table RENAME new_table;
```

RENAME TABLE, unlike ALTER TABLE, can rename multiple tables within a single statement:
```
RENAME TABLE old_table1 TO new_table1,
            old_table2 TO new_table2,
            old_table3 TO new_table3;
```

Renaming operations are performed left to right. Thus, to swap two table names, do this (assuming that a
table with the intermediary name tmp_table does not already exist):
```
RENAME TABLE old_table TO tmp_table,
            new_table TO old_table,
            tmp_table TO new_table;
```

As of MySQL 8.0.13, you can rename tables locked with a LOCK TABLES statement, provided that they
are locked with a WRITE lock or are the product of renaming WRITE-locked tables from earlier steps in a
multiple-table rename operation. For example, this is permitted:
```
LOCK TABLE old_table1 WRITE;
RENAME TABLE old_table1 TO new_table1,
new_table1 TO new_table2;
```

This is not permitted:
```
LOCK TABLE old_table1 READ;
RENAME TABLE old_table1 TO new_table1,
new_table1 TO new_table2;
```

Prior to MySQL 8.0.13, to execute RENAME TABLE, there must be no tables locked with LOCK TABLES.
With the transaction table locking conditions satisfied, the rename operation is done atomically; no other
session can access any of the tables while the rename is in progress.

If any errors occur during a RENAME TABLE, the statement fails and no changes are made.
You can use RENAME TABLE to move a table from one database to another:
```
RENAME TABLE current_db.tbl_name TO other_db.tbl_name;
```

Using this method to move all tables from one database to a different one in effect renames the database
(an operation for which MySQL has no single statement), except that the original database continues to
exist, albeit with no tables.

Like RENAME TABLE, ALTER TABLE ... RENAME can also be used to move a table to a different
database. Regardless of the statement used, if the rename operation would move the table to a database
located on a different file system, the success of the outcome is platform specific and depends on the
underlying operating system calls used to move table files.

If a table has triggers, attempts to rename the table into a different database fail with a Trigger in
wrong schema (ER_TRG_IN_WRONG_SCHEMA) error.

To rename TEMPORARY tables, RENAME TABLE does not work. Use ALTER TABLE instead.

RENAME TABLE works for views, except that views cannot be renamed into a different database.
Any privileges granted specifically for a renamed table or view are not migrated to the new name. They
must be changed manually.

RENAME TABLE changes internally generated foreign key constraint names and user-defined foreign
key constraint names that contain the string “tbl_name_ibfk_” to reflect the new table name. InnoDB
interprets foreign key constraint names that contain the string “tbl_name_ibfk_” as internally generated
names.

Foreign key constraint names that point to the renamed table are automatically updated unless there is a
conflict, in which case the statement fails with an error. A conflict occurs if the renamed constraint name
already exists. In such cases, you must drop and re-create the foreign keys for them to function properly.

## 13.1.34 TRUNCATE TABLE Syntax

```
TRUNCATE [TABLE] tbl_name
```
TRUNCATE TABLE empties a table completely. It requires the DROP privilege. Logically, TRUNCATE TABLE
is similar to a DELETE statement that deletes all rows, or a sequence of DROP TABLE and CREATE TABLE
statements.

To achieve high performance, TRUNCATE TABLE bypasses the DML method of deleting data. Thus, it
does not cause ON DELETE triggers to fire, it cannot be performed for InnoDB tables with parent-child
foreign key relationships, and it cannot be rolled back like a DML operation. However, TRUNCATE TABLE
operations on tables that use an atomic DDL-supported storage engine are either fully committed or rolled
back if the server halts during their operation. For more information, see Section 13.1.1, “Atomic Data
Definition Statement Support”.

Although TRUNCATE TABLE is similar to DELETE, it is classified as a DDL statement rather than a DML
statement. It differs from DELETE in the following ways:
• Truncate operations drop and re-create the table, which is much faster than deleting rows one by one,
particularly for large tables.
• Truncate operations cause an implicit commit, and so cannot be rolled back. See Section 13.3.3,
“Statements That Cause an Implicit Commit”.
• Truncation operations cannot be performed if the session holds an active table lock.
• TRUNCATE TABLE fails for an InnoDB table or NDB table if there are any FOREIGN KEY constraints
from other tables that reference the table. Foreign key constraints between columns of the same table
are permitted.
• Truncation operations do not return a meaningful value for the number of deleted rows. The usual result
is “0 rows affected,” which should be interpreted as “no information.”
• As long as the table definition is valid, the table can be re-created as an empty table with TRUNCATE
TABLE, even if the data or index files have become corrupted.
• Any AUTO_INCREMENT value is reset to its start value. This is true even for MyISAM and InnoDB, which
normally do not reuse sequence values.
• When used with partitioned tables, TRUNCATE TABLE preserves the partitioning; that is, the data and
index files are dropped and re-created, while the partition definitions are unaffected.
• The TRUNCATE TABLE statement does not invoke ON DELETE triggers.
• Truncating a corrupted InnoDB table is supported.

TRUNCATE TABLE for a table closes all handlers for the table that were opened with HANDLER OPEN.

TRUNCATE TABLE is treated for purposes of binary logging and replication as DROP TABLE followed by
CREATE TABLE—that is, as DDL rather than DML. This is due to the fact that, when using InnoDB and
other transactional storage engines where the transaction isolation level does not permit statement-based
logging (READ COMMITTED or READ UNCOMMITTED), the statement was not logged and replicated when
using STATEMENT or MIXED logging mode. (Bug #36763) However, it is still applied on replication slaves
using InnoDB in the manner described previously.

In MySQL 5.7 and earlier, on a system with a large buffer pool and innodb_adaptive_hash_index
enabled, a TRUNCATE TABLE operation could cause a temporary drop in system performance due
to an LRU scan that occurred when removing the table's adaptive hash index entries (Bug #68184).

The remapping of TRUNCATE TABLE to DROP TABLE and CREATE TABLE in MySQL 8.0 avoids the
problematic LRU scan.

TRUNCATE TABLE can be used with Performance Schema summary tables, but the effect is to reset
the summary columns to 0 or NULL, not to remove rows. See Section 25.11.15, “Performance Schema
Summary Tables”.

https://www.bilibili.com/video/av6358393/?p=90
