---
title: MySQL - Manual - SQL Syntax - 13.1.7 - ALTER SERVER 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - ALTER SERVER
---

MySQL `ALTER SERVER` 语法

<!--more-->

```
ALTER SERVER server_name
    OPTIONS (option [, option] ...)
```

Alters the server information for `server_name`, adjusting any of the options permitted in the `CREATE
SERVER` statement. The corresponding fields in the `mysql.servers` table are updated accordingly. This
statement requires the `SUPER` privilege.

For example, to update the `USER` option:
```
ALTER SERVER s OPTIONS (USER 'sally');
```

`ALTER SERVER` causes an implicit commit. See Section 13.3.3, “Statements That Cause an Implicit
Commit”.

`ALTER SERVER` is not written to the binary log, regardless of the logging format that is in use.

