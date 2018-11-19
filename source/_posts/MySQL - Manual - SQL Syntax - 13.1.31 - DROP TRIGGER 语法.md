---
title: MySQL - Manual - SQL Syntax - 13.1.31 - DROP TRIGGER 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - DROP TRIGGER
---

MySQL `DROP TRIGGER` 语法

<!--more-->

## 13.1.31 DROP TRIGGER Syntax

```
DROP TRIGGER [IF EXISTS] [schema_name.]trigger_name
```

这个语句删除一个触发器。模式(数据库)名称是可选的。如果省略模式，则从默认模式删除
触发器。

`DROP TRIGGER` 要求具有与触发器相关联表的 `TRIGGER` 特权。

使用 `IF EXIST` 来防止不存在的触发器发生错误。当使用 `IF EXISTS` 时，将为不存在
的触发器生成 `NOTE`。参见 Section 13.7.6.40, “SHOW WARNINGS Syntax”

如果您删除了表，表的触发器也会被删除。

