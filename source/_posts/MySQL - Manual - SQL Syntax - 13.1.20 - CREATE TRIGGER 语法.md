---
title: MySQL - Manual - SQL Syntax - 13.1.20 - CREATE TRIGGER 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - CREATE TRIGGER
---

MySQL `CREATE TRIGGER` 语法

<!--more-->

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

该语句创建一个新的触发器。

触发器是与表关联的命名数据库对象，它在表发生特定事件时激活。触发器与名为
`tbl_name` 的表关联，该表必须引用一个永久表。不能将触发器与 `TEMPORARY` 表或视图
关联。

触发器名称存在于模式名称空间中，这意味着所有触发器必须在模式中具有惟一的名称。不
同模式中的触发器可以具有相同的名称。

本节描述 `CREATE TRIGGER` 语法，更多讨论参考 Section 23.3.1, “Trigger Syntax
and Examples”

`CREATE TRIGGER` 需要与触发器关联的表的 `TRIGGER` 特权。语句可能还需要
`SET_USER_ID` 或 `SUPER` 特权，这取决于 `DEFINER` 值，如本节后面所述。如果启用了
二进制日志记录，`CREATE TRIGGER` 可能需要 `SUPER` 特权，如 Section 23.7, “
Binary Logging of Stored Programs”所述。

`DEFINER` 子句确定在触发器激活时检查访问权限时使用的安全上下文，本节稍后将对此进
行描述。

`trigger_time` 是触发器动作时间。可以是 `BEFORE` 或 `AFTER`，表示触发器在要修改
的每一行之前或之后激活。

基本列值检查发生在触发器激活之前，因此不能使用 `BEFORE` 触发器将不适合列类型的值
转换为有效值。

`trigger_event` 表示激活触发器的操作类型。这些 `trigger_event` 值是允许的:
* `INSERT`: 每当新行插入到表中时，触发器就会被激活；例如 `INSERT`, `LOAD DATA`,
  `REPLACE` 语句。
* `UPDATE`: 当一行被修改时触发，例如 `UPDATE` 语句。
* `DELETE`：从表中删除一行时触发；例如，通过 `DELETE` 和 `REPLACE` 语句。表上的
  `DROP TABLE` 和 `TRUNCATE TABLE` 语句不激活此触发器，因为它们不使用 `DELETE`。
  删除分区也不会激活此触发器。

`trigger_event` 并不表示激活触发器的 SQL 语句的文字类型，而是表示表操作的类型。
例如，`INSERT` 触发器不仅会被 `INSERT` 语句激活，而且会被 `LOAD DATA` 语句激活，
因为这两个语句都将行插入表中。

一个可能令人混淆的例子是 `INSERT INTO ... ON DUPLICATE KEY UPDATE ...` 语法：
`BEFORE INSERT` 触发器首先为每行激活，然后根据行是否有重复键，或者是一个 `AFTER
INSERT` 触发器激活，或者是 `BEFORE UPDATE` 和 `AFTER UPDATE` 两个触发器激活。

    注意：
    级联外键操作不会激活触发器。

可以为一个指定表定义多个具有相同触发事件和操作时间的触发器。

例如，一个表可以有两个 `BEFORE UPDATE` 触发器。默认情况下，具有相同触发事件和操
作时间的触发器按它们的创建顺序激活。若要影响触发器顺序，请指定一个
`trigger_order` 子句，该子句指示 `FOLLOW` 或 `PRECEDES` 以及具有相同触发器事件和
动作时间的一个现有触发器的名称。使用 `FOLLOW`，则新触发器在现有触发器之后激活。
使用 `PRECEDES`，则新触发器在现有触发器之前激活。

`trigger_body` 是触发器激活时要执行的语句。要执行多个语句，请使用 `BEGIN ...
END` 复合语句结构。这还允许您使用存储例程中允许的相同语句。参考 Section 13.6.1,
“BEGIN ... END Compound- Statement Syntax”. 一些儿语句不允许在触发器中使用，参
考 Section C.1, “Restrictions on Stored Programs”.

在触发器主体 `trigger_body` 中，可以使用别名 `OLD` 和 `NEW` 引用与触发器关联的表
中的列，`OLD.col_name` 引用的是被更新前或被删除前现有行的列名，`NEW.col_name` 引
用的是即将插入的新行或更新后的现有行的列名。

触发器不能使用 `NEW.col_name` 或 `OLD.col_name` 来引用生成的列。有关生成的列的信
息，请参见 Section 13.1.18.8, “CREATE TABLE and Generated Columns”。

MySQL 存储了在触发器创建时生效的 `sql_mode` 系统变量设置，并且总是在该设置生效后
执行触发器主体，而不管触发器开始执行时的当前服务器 SQL 模式如何。

`DEFINER` 子句指定在触发器激活时检查访问权限时使用的 MySQL 帐户。如果给定一个用
户值，它应该是一个 MySQL 帐户，指定为 `'user_name'@'host_name'`、`CURRENT_USER`
或 `CURRENT_USER()` 。默认的 `DEFINER` 值是执行 `CREATE TRIGGER` 语句的用户。这
与显式指定 `DEFINER = CURRENT_USER` 相同。

如果您指定 `DEFINER` 子句，这些规则将确定有效的 `DEFINER` 用户值：
* 如果您没有 `SET_USER_ID` 或 `SUPER` 特权，唯一允许的用户值就是您自己的帐户，可
  以按字面意思指定，也可以使用 `CURRENT_USER` 指定。您不能将 `DEFINER` 设置为其
  他帐户。
* 如果您拥有 `SET_USER_ID` 或 `SUPER` 特权，您可以指定任何语法上有效的帐户名称。
  如果指定的帐户不存在，将生成警告。
* 虽然可以用一个不存在的 `DEFINER` 帐户创建触发器，但是在该帐户实际存在之前激活
  这样的触发器并不是一个好主意。否则，与特权检查有关的行为是未定义的。

MySQL 在检查触发器权限时考虑了 `DEFINER` 用户，如下所示:
* 在 `CREATE TRIGGER` 时，执行语句的用户必须有 `TRIGGER` 特权。
* 在触发器激活时，根据 `DEFINER` 用户检查权限。该用户必须具有以下特权:
  * 主题表的 `TRIGGER` 特权。
  * 主题表的 `SELECT` 特权，如果引用表列时，在触发器主体中使用了 `OLD.col_name`
    或 `NEW.col_name`。
  * 主题表的 `UPDATE` 特权，如果表的列是触发器主体中赋值语句 `SET NEW.col_name =
    value` 的目标。
  * 触发器执行的语句通常需要的其他任何特权。

更多关于触发器安全性的信息，参考 Section 23.6, “Access Control for Stored
Programs and Views”.

在触发器主体中，`CURRENT_USER()` 函数返回用于在触发器激活时检查特权的帐户，这是
`DEFINER` 用户，不是那个因其操作导致触发器被激活的用户。有关触发器内用户审计的信
息，参考 Section 6.3.13, “SQL-Based MySQL Account Activity Auditing”.

如果您使用 `LOCK TABLES` 来锁定具有触发器的表，那么在触发器中使用的表也会被锁定
，参考 Section 13.3.6.2, “LOCK TABLES and Triggers”.

进一步讨论触发器的使用，参考 Section 23.3.1, “Trigger Syntax and Examples”.

