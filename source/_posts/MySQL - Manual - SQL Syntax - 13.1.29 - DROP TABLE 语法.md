---
title: MySQL - Manual - SQL Syntax - 13.1.29 - DROP TABLE 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - DROP TABLE
---

MySQL `DROP TABLE` 语法

<!--more-->

```
DROP [TEMPORARY] TABLE [IF EXISTS]
    tbl_name [, tbl_name] ...
    [RESTRICT | CASCADE]
```

DROP TABLE removes one or more tables. You must have the DROP privilege for each table.

Be careful with this statement! It removes the table definition and all table data. For a partitioned table, it
permanently removes the table definition, all its partitions, and all data stored in those partitions. It also
removes partition definitions associated with the dropped table.

DROP TABLE causes an implicit commit, except when used with the TEMPORARY keyword. See
Section 13.3.3, “Statements That Cause an Implicit Commit”.

    Important
    When a table is dropped, privileges granted specifically for the table are not
    automatically dropped. They must be dropped manually. See Section 13.7.1.6,
    “GRANT Syntax”.

If any tables named in the argument list do not exist, the statement fails with an error indicating by name
which nonexisting tables it was unable to drop, and no changes are made.

Use IF EXISTS to prevent an error from occurring for tables that do not exist. Instead of an error, a
NOTE is generated for each nonexistent table; these notes can be displayed with SHOW WARNINGS. See
Section 13.7.6.40, “SHOW WARNINGS Syntax”.

IF EXISTS can also be useful for dropping tables in unusual circumstances under which there is an entry
in the data dictionary but no table managed by the storage engine. (For example, if an abnormal server exit
occurs after removal of the table from the storage engine but before removal of the data dictionary entry.)

The TEMPORARY keyword has the following effects:
• The statement drops only TEMPORARY tables.
• The statement does not cause an implicit commit.
• No access rights are checked. A TEMPORARY table is visible only with the session that created it, so no
check is necessary.

Using TEMPORARY is a good way to ensure that you do not accidentally drop a non-TEMPORARY table.
The RESTRICT and CASCADE keywords do nothing. They are permitted to make porting easier from other
database systems.

DROP TABLE is not supported with all innodb_force_recovery settings. See Section 15.20.2, “Forcing
InnoDB Recovery”.

