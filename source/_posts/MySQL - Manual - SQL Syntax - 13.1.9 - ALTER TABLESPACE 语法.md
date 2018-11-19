---
title: MySQL - Manual - SQL Syntax - 13.1.9 - ALTER TABLESPACE 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - ALTER TABLESPACE
---

MySQL `ALTER TABLESPACE` 语法

<!--more-->

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

