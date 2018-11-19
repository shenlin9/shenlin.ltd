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

This statement is used to drop a stored procedure or function. That is, the specified routine is
removed from the server. You must have the ALTER ROUTINE privilege for the routine. (If the
automatic_sp_privileges system variable is enabled, that privilege and EXECUTE are granted
automatically to the routine creator when the routine is created and dropped from the creator when the
routine is dropped. See Section 23.2.2, “Stored Routines and MySQL Privileges”.)

The IF EXISTS clause is a MySQL extension. It prevents an error from occurring if the procedure or
function does not exist. A warning is produced that can be viewed with SHOW WARNINGS.

DROP FUNCTION is also used to drop user-defined functions (see Section 13.7.4.2, “DROP FUNCTION
Syntax”).


