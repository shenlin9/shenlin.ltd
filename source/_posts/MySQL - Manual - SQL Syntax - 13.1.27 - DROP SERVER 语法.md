---
title: MySQL - Manual - SQL Syntax - 13.1.27 - DROP SERVER 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - DROP SERVER
---

MySQL `DROP SERVER` 语法

<!--more-->

```
DROP SERVER [ IF EXISTS ] server_name
```

删除名为 `server_name` 的服务器定义。`mysql.servers` 表中相应的行被删除。这个语
句需要 `SUPER` 特权。

Dropping a server for a table does not affect any `FEDERATED` tables that used
this connection information when they were created
为表删除服务器不会影响创建时使用此连接信息的任何 `FEDERATED` 表。请参阅 Section
13.1.16, “CREATE SERVER Syntax”。

`DROP SERVER` 导致隐式提交。请参阅 Section 13.3.3, “Statements That Cause an
Implicit Commit”。

`DROP SERVER` 不写入二进制日志，不管使用的日志格式如何。

