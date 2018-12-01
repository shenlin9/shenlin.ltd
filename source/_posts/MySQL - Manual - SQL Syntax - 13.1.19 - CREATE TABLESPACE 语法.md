---
title: MySQL - Manual - SQL Syntax - 13.1.19 - CREATE TABLESPACE 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - CREATE TABLESPACE
---

MySQL `CREATE TABLESPACE` 语法

<!--more-->

```
CREATE TABLESPACE tablespace_name
    ADD DATAFILE 'file_name'
    [FILE_BLOCK_SIZE = value]
        [ENGINE [=] engine_name]
```

这个语句用于创建一个 InnoDB 表空间。使用 `CREATE TABLESPACE` 创建的 InnoDB 表空
间称为通用表空间。

一般的表空间是共享的表空间，类似于系统表空间，可以保存多个表，并支持所有表行格式
。通用的表空间既可以在相对于 MySQL 数据目录的位置创建，也可以在独立于 MySQL 数据
目录的位置创建。

在创建了 InnoDB 通用表空间后，可以使用 `CREATE TABLE tbl_name ...  TABLESPACE
[=] tablespace_name` 或 `ALTER TABLE tbl_name TABLESPACE [=] tablespace_name` 把
表添加到表空间。 

更多信息参考 Section 15.7.10, “InnoDB General Tablespaces”.

    注意：
    `CREATE TABLESPACE` 支持 InnoDB 引擎，在早期版本中，`CREATE TABLESPACE` 只支
    持 NDB，即 MySQL NDB Cluster 集群存储引擎。

* `ADD DATAFILE`：定义表空间数据文件的名称。

  数据文件必须用 `CREATE TABLESPACE` 语句指定，并且数据文件名必须有一个 `.bid`
  扩展名。一个 InnoDB 通用表空间只支持单独一个数据文件。

  要将数据文件放置在 MySQL 数据目录(`DATADIR`)之外的位置，请包含一个绝对目录路径
  或相对于 MySQL 数据目录的路径。如果不指定路径，通用表空间将在 MySQL 数据目录中
  创建。

  为了避免与隐式创建的每个表一个文件的表空间文件（file-per-table tablespaces）冲
  突，不支持在 MySQL 数据目录下的子目录中创建通用表空间。此外，在 MySQL 数据目录
  之外创建一个通用表空间时，该目录必须存在，并且 InnoDB 在创建表空间之前必须知道
  该目录。要使 InnoDB 知道一个未知的目录，请将该目录添加到 `innodb_directory` 参
  数值。`innodb_directory` 是一个只读启动选项。配置它需要重新启动服务器。

  `file_name`，包括路径(可选)，必须用单引号或双引号引用。文件名(不包括扩展名
  `.ibd`)和目录名的长度必须至少为一个字节。不支持零长度文件名和目录名。

* `FILE_BLOCK_SIZE`：定义表空间数据文件的块大小。

  如果不指定此选项，`FILE_BLOCK_SIZE` 默认为 `innodb_page_size`。只有在使用表空
  间存储压缩的 InnoDB 表(`ROW_FORMAT=COMPRESSED`)时，才需要 `FILE_BLOCK_SIZE` 设
  置。这种情况下创建表空间时，必须定义表空间的 `FILE_BLOCK_SIZE`。

  如果 `FILE_BLOCK_SIZE` 等于 `innodb_page_size`，那么表空间只能包含未压缩行格式
  （`COMPACT`，`REDUNDANT` 和 `DYNAMIC` 行格式）的表。具有 `COMPRESSED` 行格式的
  表与未压缩的表具有不同的物理页面大小。因此，压缩表不能与未压缩表在相同的表空间
  中共存。

  对于包含压缩表的一般表空间，必须指定 `FILE_BLOCK_SIZE` ，并且
  `FILE_BLOCK_SIZE` 值必须是与 `innodb_page_size` 值相关的有效压缩页大小。此外，
  压缩表的物理页面大小( `KEY_BLOCK_SIZE` )必须等于 `FILE_BLOCK_SIZE/1024` 。例如
  ，如果 `innodb_page_size=16K` 和 `FILE_BLOCK_SIZE=8K` ，表的 `KEY_BLOCK_SIZE`
  必须是 8。有关更多信息，请参见 Section 15.7.10, “InnoDB General Tablespaces”
  。

* `ENGINE`：定义使用表空间的存储引擎，其中 `engine_name` 是存储引擎的名称。

  目前，只支持 InnoDB 存储引擎。`ENGINE = InnoDB` 必须定义为 `CREATE TABLESPACE`
  语句的一部分，或者 InnoDB 必须定义为默认存储引擎(
  `default_storage_engine=InnoDB` )。

### Notes

* `tablespace_name` 是表空间的大小写敏感的标识符。它可以被引号引用，也可以不被引
  用。不允许使用正斜杠字符(" / ")。以 `innodb_` 开头的名称或者是不允许的，或者被
  保留作特殊用途。
* 不支持创建临时通用表空间。
* 通用表空间不支持临时表。
* `TABLESPACE` 选项可与 `CREATE TABLE` 或 `ALTER TABLE` 一起使用，以便将 InnoDB
  表分区或子分区分配给表空间(a file-per-table tablespace)。所有分区必须属于相同
  的存储引擎。不支持将表分区分配给共享的 InnoDB 表空间。共享表空间包括 InnoDB 系
  统表空间和通用表空间。
* 通用表空间支持添加任何行格式的表使用 `CREATE TABLE ...
  TABLESPACE.innodb_file_per_table` 不需要启用。
* `innodb_strict_mode` 不适用于通用表空间。表空间管理规则严格独立于
  `innodb_strict_mode` 执行。如果 `CREATE TABLESPACE` 参数不正确或不兼容，则无论
  `innodb_strict_mode` 设置如何，操作都会失败。当使用 `CREATE TABLE ...
  TABLESPACE` 或 `ALTER TABLE ... TABLESPACE` 将一个表添加到表空间后，
  `innodb_strict_mode` 被忽略，但语句的评估就像 `innodb_strict_mode` 是启用的一
  样。
* 使用 `DROP TABLESPACE` 来删除通用表空间。在删除表空间之前，必须使用 `DROP
  TABLE` 从通用表空间中删除所有表。
* 添加到通用表空间的表的所有部分都位于通用表空间中，包括索引和 `BLOB` 页。
* 与系统表空间类似，截断或删除存储在通用表空间中的表会在通用表空间 `.ibd` 数据文
  件内部创建空闲空间，但`.ibd` 数据文件只能用于新的 InnoDB 数据。空间不会释放回
  操作系统，因为它是针对每个表空间文件的（it is for file-per-table tablespaces）
  。
* 通用表空间与任何数据库或模式都没有关联。
* `ALTER TABLE ... DISCARD TABLESPACE` 和 `ALTER TABLE ...IMPORT TABLESPACE` 不
  支持属于通用表空间的表。
* 服务器对引用通用表空间的 DDL 使用表空间级别的元数据锁。相比之下，服务器对引用
  文件/表空间（file-per-table）的 DDL 使用表级别元数据锁。
* 生成的或现有的表空间不能更改为通用表空间。
* 通用表空间名称与文件/表表空间名称（file-per-table tablespace names）之间不存在
  冲突。出现在文件/表表空间名称中的 `/` 字符，不允许通用表空间名称中出现该字符。

### Examples

这个例子演示了如何创建一个通用表空间并添加三个不同行格式的未压缩表。
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

这个例子演示了如何创建一个通用表空间并添加一个压缩表。本例假设默认的
`innodb_page_size` 为 16K，`FILE_BLOCK_SIZE` 为 8192，则 `FILE_BLOCK_SIZE` 要求
压缩表的 `KEY_BLOCK_SIZE` 为 8。
```
mysql> CREATE TABLESPACE `ts2` ADD DATAFILE 'ts2.ibd' FILE_BLOCK_SIZE = 8192 Engine=InnoDB;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLE t4 (c1 INT PRIMARY KEY) TABLESPACE ts2 ROW_FORMAT=COMPRESSED KEY_BLOCK_SIZE=8;
Query OK, 0 rows affected (0.00 sec)
```
