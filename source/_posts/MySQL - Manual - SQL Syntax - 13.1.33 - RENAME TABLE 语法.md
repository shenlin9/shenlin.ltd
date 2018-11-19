---
title: MySQL - Manual - SQL Syntax - 13.1.33 - RENAME TABLE 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - RENAME TABLE
---

MySQL `RENAME TABLE` 语法

<!--more-->

```
RENAME TABLE
    tbl_name TO new_tbl_name
    [, tbl_name2 TO new_tbl_name2] ...
```

`RENAME TABLE` 可以重命名一个或多个表，你必须有原来表的 `ALTER` 和 `DROP` 特权以
及新表的 `CREATE` 和 `INSERT` 特权。

例如，使用下列语句将一个表 `old_table` 改名为 `new_table`：
```
RENAME TABLE old_table TO new_table;
```

等价于下面的 `ALTER TABLE` 语句：
```
ALTER TABLE old_table RENAME new_table;
```

`RENAME TABLE` 可以一条语句重命名多个表，`ALTER TABLE` 则不行：
```
RENAME TABLE old_table1 TO new_table1,
             old_table2 TO new_table2,
             old_table3 TO new_table3;
```

重命名操作从左到右执行，因此，可以使用下面的方法交换两个表的名字（假定作为中介的
`tmp_table` 不存在）：
```
RENAME TABLE old_table TO tmp_table,
             new_table TO old_table,
             tmp_table TO new_table;
```

在 MySQL 8.0.13 中，您可以重命名使用 `LOCK TABLES` 语句锁定的表，只要它们是被
`WRITE` 锁锁定，或者是在多个表重命名操作的早期步骤中重命名的 `WRITE` 锁定的表。
例如，这是允许的:
```
LOCK TABLE old_table1 WRITE;

RENAME TABLE old_table1 TO new_table1,
             new_table1 TO new_table2;
```

这是不允许的：
```
LOCK TABLE old_table1 READ;

RENAME TABLE old_table1 TO new_table1,
             new_table1 TO new_table2;
```

在 MySQL 8.0.13 之前，要执行 `RENAME TABLE`，表必须不能被使用 `LOCK TABLE` 锁定
。在满足事务表锁定条件的情况下，以原子方式执行重命名操作；在重命名过程中，其他会
话不能访问任何表。

如果在 `RENAME TABLE` 期间发生任何错误，则语句失败且不会作出任何更改。

您可以使用 `RENAME TABLE` 将表从一个数据库移动到另一个数据库:
```
RENAME TABLE current_db.tbl_name TO other_db.tbl_name;
```
使用此方法将所有表从一个数据库移动到另一个数据库，实际上就是重命名数据库（MySQL
没有单独语句来执行重命名数据库操作），除了原始数据库仍然存在之外(尽管没有表)。

像 `RENAME TABLE` 一样，`ALTER TABLE ... RENAME` 也可以用来将一个表移动到另一个
数据库。不管使用什么语句，如果重命名操作要将表移动到位于不同文件系统上的数据库中
，那么结果的成功取决于特定平台和用于移动表文件的底层操作系统调用。

如果一个表有触发器，则尝试通过 `RENAME TABLE` 把表改到一个不同的数据库，则会失败
并报错：`Trigger in wrong schema` (`ER_TRG_IN_WRONG_SCHEMA`)。

要重命名 `TEMPORARY` 表，`RENAME TABLE` 不起作用。用 `ALTER TABLE` 代替。

`RENAME TABLE` 适用于视图，只是视图不能通过重命名移动到另一个数据库。表或视图被
重命名后，其原有的被授予的任何特权都不会自动迁移到新名称，这些特权必须手动更改。

`RENAME TABLE` 更改了内部生成的外键约束名称和用户定义的外键约束名称，其中包含的
字符串 `tbl_name_ibfk_` 反映了新表名称。InnoDB 将包含字符串 `tbl_name_ibfk_` 的
外键约束名称解释为内部生成的名称。

除非存在冲突，否则指向重命名表的外键约束名称将自动更新，有冲突时语句会失败并报错
。如果重命名的约束名称已经存在，则会发生冲突。这种情况下，您必须删除并重新创建外
键，以便它们正常工作。
