---
title: MySQL - Manual - SQL Syntax - 13.1.23 - DROP EVENT 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - DROP EVENT
---

MySQL `DROP EVENT` 语法

<!--more-->

```
DROP EVENT [IF EXISTS] event_name
```

该语句删除了名为 `event_name` 的事件。事件将立即停止活动，并从服务器上完全删除。

如果事件不存在，则返回错误 `ERROR 1517 (HY000): Unknown event 'event_name'`。

You can override this and cause the statement to generate a warning for nonexistent events instead using `IF EXISTS`.
您可以重写此命令，并使用 `IF EXISTS` 为不存在的事件生成警告。

This statement requires the `EVENT` privilege for the schema to which the event to be dropped belongs.
该语句要求要删除的事件所属的模式具有 `EVENT` 特权。

