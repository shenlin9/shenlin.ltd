---
title: MySQL - Manual - SQL Syntax - 13.1.32 - DROP VIEW 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - DROP VIEW
---

MySQL `DROP VIEW` 语法

<!--more-->

```
DROP VIEW [IF EXISTS]
view_name [, view_name] ...
[RESTRICT | CASCADE]
```

`DROP VIEW` 可以移除一个或多个视图，你必须有每个视图的 `DROP` 特权。

如果参数列表中指定的任一视图不存在，则语句失败并报错，错误消息会指出哪个视图不存
在因此无法删除，结果就是没有任何视图被删除。

    注意
    在 MySQL 5.7 和更早的版本，如果参数列表中的任一视图不存在，则会返回错误，但
    是那些存在的视图是会被成功删除的。
    由于 MySQL 8.0 中行为的改变，MySQL 5.7 主服务器上部分完成的 `DROP VIEW` 操作
    在 MySQL 8.0 从服务器上复制失败。
    为了避免这种失败场景，可以在 `DROP VIEW` 语句中使用 `IF EXISTS` 语法来防止不
    存在的视图发生错误。
    更多信息参考 Section 13.1.1,“Atomic Data Definition Statement Support”.

`IF EXISTS` 子句可防止因不存在的视图发生错误。当给出这个子句时，会为每个不存在的
视图生成一个 `NOTE`。See Section 13.7.6.40, “SHOW WARNINGS Syntax”.

如果给出了 `RESTRICT` 和 `CASCADE`，则会被解析并忽略。

