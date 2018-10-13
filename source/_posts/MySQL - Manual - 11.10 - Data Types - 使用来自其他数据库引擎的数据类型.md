---
title: MySQL - Manual - 11.10 - Data Types - 使用来自其他数据库引擎的数据类型
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 使用来自其他数据库引擎的数据类型

<!--more-->

为了方便使用其他厂商的 SQL 实现编写的代码，MySQL 映射数据类型如下表所示。这些映
射使得从其他数据库系统导入表定义到 MySQL 变得更加容易。

    Other Vendor Type       MySQL Type
    ---                     ----
    BOOL                    TINYINT
    BOOLEAN                 TINYINT
    CHARACTER VARYING(M)    VARCHAR(M)
    FIXED                   DECIMAL
    FLOAT4                  FLOAT
    FLOAT8                  DOUBLE
    INT1                    TINYINT
    INT2                    SMALLINT
    INT3                    MEDIUMINT
    INT4                    INT
    INT8                    BIGINT
    LONG VARBINARY          MEDIUMBLOB
    LONG VARCHAR            MEDIUMTEXT
    LONG                    MEDIUMTEXT
    MIDDLEINT               MEDIUMINT
    NUMERIC                 DECIMAL

数据类型映射发生在表创建时，之后原始类型规范被丢弃。如果您使用其他供应商的类型创
建一张表，然后执行 `DESCRIBE tbl_name` 语句，MySQL 会使用等效的 MySQL 类型报告表
结构。例如:
```
mysql> CREATE TABLE t (a BOOL, b FLOAT8, c LONG VARCHAR, d NUMERIC);
Query OK, 0 rows affected (0.00 sec)

mysql> DESCRIBE t;
+-------+---------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+---------------+------+-----+---------+-------+
| a | tinyint(1) | YES | | NULL | |
| b | double | YES | | NULL | |
| c | mediumtext | YES | | NULL | |
| d | decimal(10,0) | YES | | NULL | |
+-------+---------------+------+-----+---------+-------+
4 rows in set (0.01 sec)
```
