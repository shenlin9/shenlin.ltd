---
title: MySQL - Manual - SQL Syntax - 13.1.25 - DROP INDEX 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - DROP INDEX
---

MySQL `DROP INDEX` 语法

<!--more-->

```
DROP INDEX index_name ON tbl_name
[algorithm_option | lock_option] ...
algorithm_option:
ALGORITHM [=] {DEFAULT|INPLACE|COPY}
lock_option:
LOCK [=] {DEFAULT|NONE|SHARED|EXCLUSIVE}
```

DROP INDEX drops the index named index_name from the table tbl_name. This statement is mapped to
an ALTER TABLE statement to drop the index. See Section 13.1.8, “ALTER TABLE Syntax”.

To drop a primary key, the index name is always PRIMARY, which must be specified as a quoted identifier
because PRIMARY is a reserved word:
```
DROP INDEX `PRIMARY` ON t;
```

ALGORITHM and LOCK clauses may be given to influence the table copying method and level of
concurrency for reading and writing the table while its indexes are being modified. They have the same
meaning as for the ALTER TABLE statement. For more information, see Section 13.1.8, “ALTER TABLE
Syntax”

