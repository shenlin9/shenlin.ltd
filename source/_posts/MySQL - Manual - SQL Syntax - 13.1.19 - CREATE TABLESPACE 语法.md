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

