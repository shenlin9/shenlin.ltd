---
title: MySQL - Manual - SQL Syntax - 13.1.30 - DROP TABLESPACE 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - DROP TABLESPACE
---

MySQL `DROP TABLESPACE` 语法

<!--more-->

```
DROP TABLESPACE tablespace_name
    [ENGINE [=] engine_name]
```

此语句用于删除使用 `CREATE TABLESPACE` 语法创建的 InnoDB 通用表空间。( Section
13.1.19, “CREATE TABLESPACE Syntax”)

在 `DROP TABLESPACE` 操作之前，所有表都必须从表空间中删除。如果表空间不是空的，
`DROP TABLESPACE` 将返回一个错误。

`tablespace_name`: 是 MySQL 中区分大小写的标识符。

`ENGINE`: 定义使用表空间的存储引擎，其中 `engine_name` 是存储引擎的名称。目前，
只支持 InnoDB 存储引擎。

    注意
    不赞成使用 `ENGINE` 子句，因为将在以后的版本中删除。表空间存储引擎根据数据字
    典来判断，这使得 `ENGINE` 子句过时。

### 注意

* 当删除表空间中的最后一个表后，一般不会自动删除 InnoDB 表空间。必须使用 `DROP
  TABLESPACE tablespace_name` 显式删除表空间。

* `DROP DATABASE` 操作可以删除属于一般表空间的表，但不能删除表空间，即使操作删除
  了属于表空间的所有表。必须使用 `DROP TABLESPACE tablespace_name` 显式删除表空
  间。

* 与系统表空间类似，存储在一般表空间中的表被截断或删除，会在一般表空间 `.ibd` 数
  据文件内部创建空闲空间，这些空间只能用于新的 InnoDB 数据。空间不会释放回操作系
  统，因为它是针对每个表的文件表空间的(it is for file-per-table tablespaces)。

### 例子

这个例子演示了如何删除 InnoDB 通用表空间。一般的表空间 `ts1` 是用一个表创建的。
在删除表空间之前，必须删除表。
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

