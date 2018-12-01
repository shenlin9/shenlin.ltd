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

`DROP INDEX` 从表 `tbl_name` 里删除索引 `index_name`，这个语句被映射到一个
`ALTER TABLE` 语句以删除索引。
Section 13.1.8, “ALTER TABLE Syntax”.

要删除主键，索引名永远是 `PRIMARY`，必须使用引号引起来，因为 `PRIMARY` 是一个保
留字:
```
DROP INDEX `PRIMARY` ON t;
```

`ALGORITHM` 和 `LOCK` 子句可以在修改表索引的同时影响表的复制方法和并发性水平，它
们的含义与 `ALTER TABLE` 语句中的相同。
更多信息参考 Section 13.1.8, “ALTER TABLE Syntax”

