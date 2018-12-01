---
title: MySQL - Manual - SQL Syntax - 13.1.29 - DROP TABLE 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - DROP TABLE
---

MySQL `DROP TABLE` 语法

<!--more-->

```
DROP [TEMPORARY] TABLE [IF EXISTS]
    tbl_name [, tbl_name] ...
    [RESTRICT | CASCADE]
```

`DROP TABLE` 删除一个或多个表。您必须拥有每个表的 `DROP` 特权。

小心使用这条语句！它删除了表的定义和所有表数据。对于分区表，它将永久删除表定义、
所有分区以及存储在这些分区中的所有数据。它还删除了与表关联的分区定义。

`DROP TABLE` 会导致隐式提交，除非与 `TEMPORARY` 关键字一起使用。参考 Section
13.3.3, “Statements That Cause an Implicit Commit”。

    重要
    在删除表时，不会自动删除为表授予的特权。它们必须手动删除。
    See Section 13.7.1.6,“GRANT Syntax”.

如果参数列表中指定的表不存在，则该语句将失败，并出现错误，指出它无法删除的不存在
的表的名称，并且最终不做任何更改。

使用 `IF EXISTS` 可以防止因不存在的表发生错误。则每个不存在的表不生成错误，而是
生成 `NOTE`；这些 `NOTE` 可以用 `SHOW WARNINGS` 显示出来。参考 See Section
13.7.6.40,“SHOW WARNINGS Syntax”.

`IF EXISTS` 对于在数据字典中有一个条目但没有对应的表被存储引擎管理的特殊情况下删
除表也很有用。（例如，如果在从存储引擎删除表之后，但在删除数据字典条目之前发生了
异常的服务器退出。）

`TEMPORARY` 关键字有以下效果：
* 语句只删除 `TEMPORARY` 表
* 语句不会导致隐式提交
* 不检查访问权限。只有在创建它的会话才可见 `TEMPORARY` 表，因此没有必要检查。

使用 `TEMPORARY` 是确保您不会意外删掉一个非 `TEMPORARY` 表的好方法。

`RESTRICT` 和 `CASCADE` 关键字不起作用。它们可以使从其他数据库系统进行移植变得更
容易。

`DROP TABLE` 不支持所有的 `innodb_force_recovery` 设置。参考 Section 15.20.2, “
Forcing InnoDB Recovery”.

