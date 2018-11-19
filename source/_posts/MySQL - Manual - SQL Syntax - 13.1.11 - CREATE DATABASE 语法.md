---
title: MySQL - Manual - SQL Syntax - 13.1.11 - CREATE DATABASE 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - CREATE DATABASE
---

MySQL `CREATE DATABASE` 语法

<!--more-->

```
CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] db_name
    [create_specification] ...

create_specification:
[DEFAULT] CHARACTER SET [=] charset_name
| [DEFAULT] COLLATE [=] collation_name
```

CREATE DATABASE creates a database with the given name. To use this statement, you need the CREATE
privilege for the database. CREATE SCHEMA is a synonym for CREATE DATABASE.

An error occurs if the database exists and you did not specify IF NOT EXISTS.

CREATE DATABASE is not permitted within a session that has an active LOCK TABLES statement.

create_specification options specify database characteristics. Database characteristics are stored
in the data dictionary. The CHARACTER SET clause specifies the default database character set. The
COLLATE clause specifies the default database collation. Chapter 10, Character Sets, Collations, Unicode,
discusses character set and collation names.

A database in MySQL is implemented as a directory containing files that correspond to tables in the
database. Because there are no tables in a database when it is initially created, the CREATE DATABASE
statement creates only a directory under the MySQL data directory. Rules for permissible database
names are given in Section 9.2, “Schema Object Names”. If a database name contains special characters,
the name for the database directory contains encoded versions of those characters as described in
Section 9.2.3, “Mapping of Identifiers to File Names”.

Creating a database directory by manually creating a directory under the data directory (for example, with
mkdir) is temporarily unsupported in MySQL 8.0.0.

You can also use the mysqladmin program to create databases. See Section 4.5.2, “mysqladmin —
Client for Administering a MySQL Server”.

