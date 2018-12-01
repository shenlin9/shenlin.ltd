---
title: MySQL - Manual - SQL Syntax - 13.1.28 - DROP SPATIAL REFERENCE SYSTEM 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - DROP SPATIAL REFERENCE SYSTEM
---

MySQL `DROP SPATIAL REFERENCE SYSTEM` 语法

<!--more-->

```
DROP SPATIAL REFERENCE SYSTEM
     [IF EXISTS]
     srid

srid: 32-bit unsigned integer
```

该语句从数据字典中删除 `spatial reference system (SRS)` 空间参考系统(SRS)定义。
它需要 `SUPER` 特权。

例如：
```
DROP SPATIAL REFERENCE SYSTEM 4120;
```

如果不存在具有 SRID 值的 SRS 定义，除非指定了 `IF EXISTS`，否则将出现错误。在指
定了 `IF EXISTS` 后，会出现警告而不是错误：

如果现有表中的某个列使用 SRID 值，则会发生错误。例如:
```
mysql> DROP SPATIAL REFERENCE SYSTEM 4326;
ERROR 3716 (SR005): Can't modify SRID 4326.
There is at least one column depending on it.
```

要确定哪个列或哪些列使用 SRID，请使用以下查询：
```
SELECT * FROM INFORMATION_SCHEMA.ST_GEOMETRY_COLUMNS WHERE SRS_ID=4326;
```

SRID 值必须在 32 位无符号整数的范围内，有以下限制:
* SRID 0 是一个有效的 SRID 但是不能和 `DROP SPATIAL REFERENCE SYSTEM` 一起使用
* 如果值在保留的 SRID 范围内，就会出现警告。预留范围为 `[0,32767]`(EPSG 预留)，
  `[60,000,000 , 69,999,999]`(EPSG 预留)，`[2,000,000,000 ,
  2,147,483,647]`(MySQL 预留)。EPSG 代表 European Petroleum Survey Group 欧洲石
  油调查组织。
* 用户不应删除保留范围内 SRSs 与 SRIDs。如果删除了系统安装的 SRSs，则可以重新创
  建 SRS 定义以进行 MySQL 升级。


