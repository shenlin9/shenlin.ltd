---
title: MySQL - Manual - SQL Syntax - 13.1.26 - DROP PROCEDURE 和 DROP FUNCTION 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - DROP FUNCTION
 - DROP PROCEDURE
---

MySQL `DROP FUNCTION` 语法

<!--more-->

```
DROP {PROCEDURE | FUNCTION} [IF EXISTS] sp_name
```

该语句用于删除存储过程或函数。也就是说，从服务器中删除指定的例程。您必须拥有例程
的 `ALTER ROUTINE` 特权。（如果启用了 `automatic_sp_privileges` 系统变量，当例程
创建时，`ALTER ROUTINE` 和 `EXECUTE` 特权将自动授予例程创建者，当删除例程时，授
予的特权将自动从例程创建者删除。）参见 Section 23.2.2, “Stored Routines and
MySQL Privileges”。

`IF EXISTS` 子句是一个 MySQL 扩展。如果过程或函数不存在，它可以防止错误发生。可
以使用 `SHOW WARNINGS` 查看生成的警告。

`DROP FUNCTION` 也用于删除用户定义的函数 (Section 13.7.4.2, “DROP FUNCTION
Syntax”)。


