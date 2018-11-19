---
title: MySQL - Manual - SQL Syntax - 13.1.22 - DROP DATABASE 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - DROP DATABASE
---

MySQL `DROP DATABASE` 语法

<!--more-->

```
DROP {DATABASE | SCHEMA} [IF EXISTS] db_name
```

DROP DATABASE drops all tables in the database and deletes the database. Be very careful with this
statement! To use DROP DATABASE, you need the DROP privilege on the database. DROP SCHEMA is a
synonym for DROP DATABASE.

    Important
    When a database is dropped, privileges granted specifically for the database are
    not automatically dropped. They must be dropped manually. See Section 13.7.1.6,
    “GRANT Syntax”.

IF EXISTS is used to prevent an error from occurring if the database does not exist.

If the default database is dropped, the default database is unset (the DATABASE() function returns NULL).
If you use DROP DATABASE on a symbolically linked database, both the link and the original database are
deleted.

DROP DATABASE returns the number of tables that were removed.

The DROP DATABASE statement removes from the given database directory those files and directories that
MySQL itself may create during normal operation. This includes all files with the extensions shown in the
following list:
• .BAK
• .DAT
• .HSH
• .MRG
• .MYD
• .MYI
• .cfg
• .db
• .ibd
• .ndb

If other files or directories remain in the database directory after MySQL removes those just listed, the
database directory cannot be removed. In this case, you must remove any remaining files or directories
manually and issue the DROP DATABASE statement again.

Dropping a database does not remove any TEMPORARY tables that were created in that database.
TEMPORARY tables are automatically removed when the session that created them ends. See
Section 13.1.18.3, “CREATE TEMPORARY TABLE Syntax”.

You can also drop databases with mysqladmin. See Section 4.5.2, “mysqladmin — Client for
Administering a MySQL Server”.

