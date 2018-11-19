---
title: MySQL - Manual - SQL Syntax - 13.1.5 - ALTER INSTANCE 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - ALTER INSTANCE
---

MySQL `ALTER INSTANCE` 语法

<!--more-->

```
ALTER INSTANCE ROTATE INNODB MASTER KEY
```

`ALTER INSTANCE` defines actions applicable to a MySQL server instance.

The `ALTER INSTANCE ROTATE INNODB MASTER KEY` statement is used to rotate the master
encryption key used for InnoDB tablespace encryption. A keyring plugin must be loaded to use this
statement. By default, the MySQL server loads the `keyring_file` plugin. Key rotation requires the
`ENCRYPTION_KEY_ADMIN` or `SUPER` privilege.

`ALTER INSTANCE ROTATE INNODB MASTER KEY` supports concurrent DML. However, it cannot be run
concurrently with `CREATE TABLE ... ENCRYPTION` or `ALTER TABLE ... ENCRYPTION` operations,
and locks are taken to prevent conflicts that could arise from concurrent execution of these statements. If
one of the conflicting statements is running, it must complete before another can proceed.

`ALTER INSTANCE` actions are written to the binary log so that they can be executed on replicated servers.

For additional `ALTER INSTANCE ROTATE INNODB MASTER KEY` usage information, see Section 15.7.11, “InnoDB Tablespace Encryption”. For information about the keyring_file plugin, see Section 6.5.4, “The MySQL Keyring”.

