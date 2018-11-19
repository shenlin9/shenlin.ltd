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

Drops the server definition for the server named server_name. The corresponding row in the
mysql.servers table is deleted. This statement requires the SUPER privilege.

Dropping a server for a table does not affect any FEDERATED tables that used this connection information
when they were created. See Section 13.1.16, “CREATE SERVER Syntax”.

DROP SERVER causes an implicit commit. See Section 13.3.3, “Statements That Cause an Implicit
Commit”.

DROP SERVER is not written to the binary log, regardless of the logging format that is in use.

