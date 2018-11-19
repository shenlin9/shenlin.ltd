---
title: MySQL - Manual - SQL Syntax - 13.1.10 - ALTER VIEW 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - ALTER VIEW
---

MySQL `ALTER VIEW` 语法

<!--more-->

```
ALTER
    [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    [DEFINER = { user | CURRENT_USER }]
    [SQL SECURITY { DEFINER | INVOKER }]
VIEW view_name [(column_list)]
AS select_statement
    [WITH [CASCADED | LOCAL] CHECK OPTION]
```

This statement changes the definition of a view, which must exist. The syntax is similar to that for `CREATE VIEW` see Section 13.1.21, “CREATE VIEW Syntax”). This statement requires the `CREATE VIEW` and `DROP` privileges for the view, and some privilege for each column referred to in the `SELECT` statement.  `ALTER VIEW` is permitted only to the definer or users with the `SET_USER_ID` or `SUPER` privilege.

