---
title: MySQL - Manual - SQL Syntax - 13.1.22 - DROP DATABASE 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - DROP DATABASE
---

MySQL `DROP DATABASE` 语法

<!--more-->

```
DROP {DATABASE | SCHEMA} [IF EXISTS] db_name
```

`DROP DATABASE` 将删除数据库中的所有表后并删除数据库。对这个语句要非常小心!
要使用 `DROP DATABASE`，您需要对应数据库的 `DROP` 特权。`DROP SCHEMA` 是 `DROP
DATABASE` 的同义词。

    重要
    当一个数据库被删除后，不会自动删除为数据库授予的特权。它们必须手动删除。
    参考 Section 13.7.1.6,“GRANT Syntax”.

`IF EXISTS` 用于防止数据库不存在时发生错误。

如果删除的是默认数据库，则默认数据库变为未设置，`DATABASE()` 函数返回 `NULL`。

如果把 `DROP DATABASE` 用于符号链接数据库，则符号链接和原数据库都会被删除。

`DROP DATABASE` 返回被移除表的数量。

`DROP DATABASE` 语句从给定的数据库目录中删除 MySQL 在正常操作期间可能创建的文件
和目录，包括了所有扩展名如下表所示的文件：
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

如果 MySQL 删除刚才列出的文件或目录后，数据库目录中还保留其他文件或目录，则无法
删除数据库目录。在这种情况下，您必须手动删除任何剩余的文件或目录，并再次执行
`DROP DATABASE` 语句。

删除数据库不会删除在该数据库中创建的任何 `TEMPORARY` 表。`TEMPORARY` 表在创建它
们的会话结束时会自动删除。Section 13.1.18.3, “CREATE TEMPORARY TABLE Syntax”.

还可以使用 `mysqladmin` 命令删除数据库，参考 Section 4.5.2, “mysqladmin —
Client for Administering a MySQL Server”.

