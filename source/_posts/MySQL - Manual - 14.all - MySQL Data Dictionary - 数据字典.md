---
title: MySQL - Manual - 14.all - MySQL Data Dictionary - 数据字典
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 数据字典

<!--more-->

MySQL 服务器包含了一个事务数据字典，存储了关于数据库对象的信息。在以前的 MySQL
版本中，字典数据存储在元数据文件、非事务性表和存储引擎特定的数据字典中。

本章描述了数据字典的主要特性、优点、使用差异和限制。关于数据字典特性的其他含义参
考 ![MySQL 8.0 Release Notes](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/)
的 “Data Dictionary Notes”章节。

MySQL 数据词典的优点包括：
* 统一存储字典数据的集中式数据字典模式的简单性，参考 Section 14.1,“Data
  Dictionary Schema”.
* 移除基于文件的元数据存储。参考 Section 14.2, “Removal of File-based Metadata
  Storage”.
* 事务性的、崩溃安全（crash-safe）的字典数据存储，参考 Section 14.3, “
  Transactional Storage of Dictionary Data”.
* 对字典对象的统一和集中缓存，参考  Section 14.4, “Dictionary Object Cache”.
* 对某些 `INFORMATION_SCHEMA` 表更简单和改进的实现，参考  Section 14.5,“
  INFORMATION_SCHEMA and Data Dictionary Integration”.
* 原子 DDL，参考  Section 13.1.1, “Atomic Data Definition Statement Support”.

    重要：
    与没有数据字典的服务器相比，支持数据字典的服务器需要一些一般操作上的差异；参
    见 Section 14.7, “Data Dictionary Usage Differences”。另外，对于升级到
    MySQL 8.0，升级过程与之前的 MySQL 版本有所不同，要求您通过检查特定的先决条件
    来验证安装的 MySQL 的升级就绪情况。更多信息参考 Section 2.10.1, “Upgrading
    MySQL”，特别是 Section 2.10.1.1 MySQL Upgrade Strategies - Verifying
    Upgrade Prerequisites for Your MySQL 5.7 Installation。

## 14.1 Data Dictionary Schema 

数据字典表是受保护的，并且只能在 MySQL 的调试构建中访问。然而，MySQL 支持通过
`INFORMATION_SCHEMA` 表和 `SHOW` 语句访问存储在数据词典表中的数据。关于组成数据
字典的表的概述，请参阅 Section 5.3 The mysql System Database -- Data Dictionary
Tables。

MySQL 系统表仍然存在于 MySQL 8.0 中，可以通过在 `mysql` 系统数据库上执行 `SHOW
TABLES` 语句来查看。一般来说，MySQL 系统表和数据字典表之间的区别在于，系统表包含
辅助数据，比如时区和帮助信息，而数据字典表包含执行 SQL 查询所需的数据。MySQL 系
统表和数据字典表在如何升级方面也有所不同。升级 MySQL 系统表需要运行
`mysql_upgrade` 。数据字典升级是由 MySQL 服务器管理的。具体请查看下面的 How the
Data Dictionary is Upgraded。

### How the Data Dictionary is Upgraded

新版本的 MySQL 可能对数据字典表定义进行了更改。这些变化出现在新安装的 MySQL 版本
中，但是当对 MySQL 二进制文件进行就地升级并且 MySQL 服务器使用新的二进制文件重新
启动时会应用更改。在启动时，将服务器的数据字典版本与存储在数据字典中的版本信息进
行比较，以确定是否应该升级数据字典表。如果需要升级，并且支持升级，服务器就会用更
新过的定义创建数据字典表，将持久的元数据复制到新的表中，自动用新表替换旧表，并重
新初始化数据字典。如果没有必要升级，则继续启动不更新数据字典表。

数据字典表的升级是一个原子操作，这意味着所有的数据字典表都是根据需要进行升级的，
或者操作失败。如果升级操作失败，服务器启动就会出现错误。在这种情况下，旧的服务器
二进制文件可以与旧的数据目录一起使用来启动服务器。当新的服务器二进制文件再次被用
于启动服务器时，将重新尝试数据字典升级。

一般来说，在成功升级了数据字典表之后，不可能使用旧的服务器二进制文件重新启动服务
器。因此，在升级了数据字典表之后，不支持将 MySQL 服务器二进制文件降级为以前的
MySQL 版本。

`mysqld --no-dd-upgrade` 选项可用于防止在启动时自动升级数据字典表。当
`--no-dd-upgrade` 被指定时，服务器发现服务器的数据字典版本与存储在数据字典中的版
本不同，则启动失败，错误消息会表明禁止数据字典升级。

### Viewing Data Dictionary Tables Using a Debug Build of MySQL

数据字典表在默认情况下是受保护的，但是可以通过编译 MySQL 时使用调试支持（使用
CMake 选项 `-DWITH_DEBUG=1`）并指定 `+d,skip_dd_table_access_check debug` 选项和
修饰符来访问。有关编译调试构建的信息，see Section 28.5.1.1, “Compiling MySQL
for Debugging”.

    警告：
    不建议直接修改或写入数据字典表，可能会使您的 MySQL 实例无法操作。

在使用调试支持编译 MySQL 之后，请使用这个 SET 语句使 MySQL 客户端会话可见数据词
典表：
```
mysql> SET SESSION debug='+d,skip_dd_table_access_check';
```

使用这个查询来检索数据字典表的列表：
```
mysql> SELECT name, schema_id, hidden, type FROM mysql.tables where schema_id=1
AND hidden='System';
```

使用 `SHOW CREATE TABLE` 查看数据字典表的定义，例如：
```
mysql> SHOW CREATE TABLE mysql.catalogs\G
```

## 14.2 Removal of File-based Metadata Storage 

在以前的 MySQL 版本中，字典数据部分存储在元数据文件中。基于文件的元数据存储的问
题包括昂贵的文件扫描、对文件系统相关的 bug 的敏感性、处理复制的的复杂代码和崩溃
恢复失败状态，以及缺乏可扩展性，使得为新特性和关系对象添加元数据变得困难。

下面列出的元数据文件已经从 MySQL 中删除。除非另有说明，以前存储在元数据文件中的
数据现在存储在数据字典表中。
* `.frm` 文件: 表的元数据文件，移除 `.frm` 文件:
    * 去掉了 `.frm` 文件结构所施加的表定义的 64KB 大小限制。
    * `INFORMATION_SCHEMA.TABLES VERSION` 列报告一个硬编码的值为 10，这是 MySQL
      5.7 中使用的最后一个 `frm` 文件版本。
* `.par` 文件: 分区定义文件。InnoDB 在 MySQL 5.7 中停止使用分区定义文件，引入了
  InnoDB 表的本机分区支持。
* `.TRN` 文件: 触发器名称空间文件
* `.TRG` 文件: 触发器参数文件
* `.isl` 文件: InnoDB符号链接文件，其中包含在数据目录之外创建的每个表表空间文件
  的位置。
* `db.opt` 文件: 数据库配置文件。每个数据库目录一套这些文件，包含数据库默认字符
  集属性。

## 14.3 Transactional Storage of Dictionary Data 

数据字典模式在事务性（InnoDB）表中存储字典数据。数据字典表与非数据字典系统表一起
位于 `mysql` 数据库中。

数据字典表是在一个单独的名为 `mysql.ibd` 的 InnoDB 表空间中创建的，此表空间驻留
在 MySQL 数据目录中。`mysql.ibd` 的表空间文件必须驻留在 MySQL 数据目录中，它的名
称不能被修改或被另一个表空间使用。

字典数据和存储在 InnoDB 表中的用户数据受同一套系统的保护：相同的提交、回滚和奔溃
恢复功能。

## 14.4 Dictionary Object Cache 

字典对象缓存是一个共享的全局缓存，它存储以前访问过的内存中的数据字典对象，以支持
对象重用和最小化磁盘输入输出。与 MySQL 所使用的其他缓存机制类似，字典对象缓存使
用基于 LRU 的驱逐策略，将最近使用的对象从内存中驱逐出去。

字典对象缓存包含存储不同对象类型的缓存分区。一些缓存分区大小限制是可配置的，而其
他的则是硬编码的。

* **表空间定义缓存分区**：存储表空间定义对象。`tablespace_definition_cache` 选项
  为可以存储在字典对象缓存中的表空间定义对象的数量设置了限制。默认值是 256。

* **模式定义缓存分区**：存储模式定义对象。`schema_definition_cache` 选项为可以存
  储在字典对象缓存中的模式定义对象的数量设置了限制。默认值是 256。

* **表定义缓存分区**：储存表定义对象。对象的限制被设置为 `max_connections` 的值
  ，该值的缺省值为 151。

  表定义缓存分区与使用 `table_definition_cache` 配置选项配置的表定义缓存并行存在
  。这两个缓存都存储表定义，但服务于 MySQL 服务器的不同部分。一个缓存中的对象不
  依赖于另一个缓存中的对象的存在。

* **存储程序定义缓存分区**：存储着存储程序的定义对象。
  `stored_program_definition_cache` 选项设置了可以储存在字典对象缓存中的存储程序
  定义对象的数量限制。默认值是 256。

  存储程序定义缓存分区与使用 `stored_program_cache` 选项配置的存储过程、存储函数
  缓存并行存在。

  `stored_program_cache` 选项为每个连接缓存的存储过程或函数的数量设置了一个软上
  限，每次连接执行一个存储过程或函数时，都会检查这个限制。另一方面，存储程序定义
  缓存分区是一个共享的缓存，它存储程序定义对象是为其他目的。存储程序定义缓存分区
  中的对象的存在不依赖于存储过程缓存或存储函数缓存中的对象的存在，反之亦然。

* **字符集定义缓存分区**：存储字符集定义对象，并且以硬编码方式限制了对象数为 256。

* **校对规则定义缓存分区**：存储校对规则定义对象，并且以硬编码方式限制了对象数为
  256。

有关字典对象缓存配置选项的有效值的信息，请参阅 Section 5.1.7,“Server System
Variables”

## 14.5 INFORMATION_SCHEMA and Data Dictionary Integration 

随着数据字典的引入，下面的 `INFORMATION_SCHEMA` 表以数据字典表视图的方式实现：

* CHARACTER_SETS
* COLLATIONS
* COLLATION_CHARACTER_SET_APPLICABILITY
* COLUMNS
* COLUMN_STATISTICS
* EVENTS
* FILES
* INNODB_COLUMNS
* INNODB_DATAFILES
* INNODB_FIELDS
* INNODB_FOREIGN
* INNODB_FOREIGN_COLS
* INNODB_INDEXES
* INNODB_TABLES
* INNODB_TABLESPACES
* INNODB_TABLESPACES_BRIEF
* INNODB_TABLESTATS
* KEY_COLUMN_USAGE
* KEYWORDS
* PARAMETERS
* PARTITIONS
* REFERENTIAL_CONSTRAINTS
* RESOURCE_GROUPS
* ROUTINES
* SCHEMATA
* STATISTICS
* ST_GEOMETRY_COLUMNS
* ST_SPATIAL_REFERENCE_SYSTEMS
* TABLES
* TABLE_CONSTRAINTS
* TRIGGERS
* VIEWS
* VIEW_ROUTINE_USAGE
* VIEW_TABLE_USAGE

现在对这些表的查询更加高效，因为它们从数据字典表中获取信息，而不是通过其他较慢的
方法获取信息。特别地，对于每个 `INFORMATION_SCHEMA` 表，都是数据字典表的视图：

* 服务器不必再为 `INFORMATION_SCHEMA` 表的每个查询创建一个临时表。

* 当底层的数据字典表存储以前通过目录扫描获得的值（例如，枚举数据库名或库中的表名
  ）或文件打开操作（例如，从 `.frm` 文件中读取信息）时，`INFORMATION_SCHEMA` 查
  询这些值现在使用表查找代替了。（另外，即使对于一个非视图的
  `INFORMATION_SCHEMA` 表，数据库和表名之类的值也会通过数据字典中的查找来获取，
  不再需要扫描目录或文件。）

* 底层数据字典表上的索引允许优化器构造高效的查询执行计划，以前的实现不是这样的，
  它处理 `INFORMATION_SCHEMA` 表的每个查询是使用临时表。

前面的改进还适用于 `SHOW` 系列语句，这些语句显示与数据字典表上的视图相对应的
`INFORMATION_SCHEMA` 表的信息。例如，`SHOW DATABASES` 显示的信息与 `SCHEMATA` 表
相同。

除了在数据字典表中引入视图之外，`STATISTICS` 和 `TABLES` 表中包含的表统计信息现
在被缓存以改进 `INFORMATION_SCHEMA` 查询性能。

系统变量 `information_schema_stats_expiry` 定义了缓存的表统计信息过期的时间周期
。默认值是 86400 秒（24 小时）。如果没有缓存的统计数据或统计数据过期，则在查询表
统计列时从存储引擎检索统计信息。

要对某个给定的表随时更新其缓存值，可以设置 `ANALYZE TABLE
information_schema_stats_expiry` 为 0，使得对 `INFORMATION_SCHEMA` 的查询直接从
存储引擎检索最新的统计信息，不过这样并不会像检索缓存的统计数据那样快。

更多信息，参考 Section 8.2.3, “Optimizing INFORMATION_SCHEMA Queries”.

## 14.6 Serialized Dictionary Information (SDI) 

除了在数据字典中存储关于数据库对象的元数据之外，MySQL 还将其存储为序列化形式。这
个数据被称为序列化字典信息（Serialized Dictionary Information, SDI）。InnoDB 在
其表空间文件中存储 SDI 数据。其他存储引擎将 SDI 数据存储在模式目录下的 `.sdi` 文
件中。SDI 数据是以一种紧凑的 JSON 格式生成的。

序列化字典信息（SDI）在所有 InnoDB 表空间文件中都存在，除了临时表空间文件和撤销
表空间文件。InnoDB 表空间文件中的 SDI 记录只描述表和包含在表空间中的表空间对象。

在 InnoDB 表空间文件内的 SDI 数据仅由表空间中的表上的 DDL 操作进行更新。

SDI 数据的存在提供了元数据冗余。例如，如果数据字典不可用，则可以使用 `ibd2sdi`
工具直接从 InnoDB 表空间文件中提取对象元数据。

对于 InnoDB 来说，SDI 记录需要一个单独的索引页，默认情况下大小是 16k。然而，SDI
数据被压缩以减少存储占用。对于由多个表空间组成的分区 InnoDB 表，SDI 数据存储在第
一个分区的表空间文件中。

MySQL 服务器使用在 DDL 操作时使用的内部 API 来创建和维护 SDI 记录。

`IMPORT TABLE` 语句根据 `.sdi` 文件中包含的信息导入 MyISAM 表。要了解更多信息，
请参阅 Section 13.2.5, “IMPORT TABLE Syntax”。

## 14.7 Data Dictionary Usage Differences 

与没有数据词典的服务器相比，使用数据字典支持的 MySQL 服务器需要一些操作上的差异：

* 以前，启用 `innodb_read_only` 系统变量可以阻止仅为 InnoDB 存储引擎创建表和删除
  表。从 MySQL 8.0 起，启用 `innodb_read_only` 可以阻止在所有存储引擎进行创建表
  和删除表这些操作。任何存储引擎创建表和删除表的操作都会修改 `mysql` 系统数据库
  里的数据字典表，但是当启用 `innodb_read_only` 时，使用 InnoDB 存储引擎的这些表
  不能被修改。同样的原则也适用于需要修改数据字典表的其他表操作。例子:

  * `ANALYZE TABLE` 执行失败，是因为此操作更新了表统计信息，这些数据存储在数据字
    典中。

  * `ALTER TABLE tbl_name ENGINE=engine_name` 执行失败，是因为此操作更新了存储引
    擎的名称，而名称也存储在数据字典中。

  注意：
  启用 `innodb_read_only` 对系统数据库 `mysql` 中的非数据字典表也有重要的影响，
  细节可参考 Section 15.13, “InnoDB Startup Options and System Variables”种的
  `innodb_read_only` 描述。

* 以前，系统数据库 `mysql` 中的表对 DML 和 DDL 语句是可见的。从 MySQL 8.0 起，数
  据字典表是不可见的，不能直接修改或查询。然而，在大多数情况下，都有相应的
  `INFORMATION_SCHEMA` 表可以作为替代来查询。这使得底层的数据字典表可以随着服务
  器的继续开发而改变，同时维护了一个稳定的 `INFORMATION_SCHEMA` 接口供应用程序使
  用。

* MySQL 8.0 中 `INFORMATION_SCHEMA` 表和数据字典紧密联系起来，导致在几个使用方面
  的不同：

  * 以前，直接从存储引擎获取 `INFORMATION_SCHEMA` 的 `STATISTICS` 和 `TABLES` 表
    里的表统计信息，从 MySQL 8.0 起，默认使用缓存的表统计信息。系统变量
    `information_schema_stats_expiry` 定义了缓存的表统计信息过期的时间周期。默认
    值是 86400 秒（24 小时）（要在任意时候更新指定表的缓存数据，使用 `ANALYZE
    TABLE`）。如果没有缓存的统计数据或统计数据过期，则在查询表统计列时从存储引擎
    检索统计信息。要直接从存储引擎检索最新的统计信息，则设置
    `information_schema_stats_expiry` 为 0。更多信息，参考 Section 8.2.3, “
    Optimizing INFORMATION_SCHEMA Queries”.

  * 几个 `INFORMATION_SCHEMA` 表是数据字典表上的视图，它使优化器能够在这些底层表
    上使用索引。因此，根据优化器的选择，`INFORMATION_SCHEMA` 查询结果的行顺序可
    能与以前的结果不同。如果查询结果必须具有特定的行排序特征，则请使用 `ORDER
    BY` 子句。

  * `mysqldump` 和 `mysqlpump` 不再转储 `INFORMATION_SCHEMA` 数据库，即使在命令
    行上明确的指定了此数据库。

  * `CREATE TABLE dst_tbl LIKE src_tbl` 要求 `src_tbl` 是一个基表，如果
    `src_tbl` 是一个 `INFORMATION_SCHEMA` 表，即实际上是数据字典表的视图，则会执
    行失败。

  * 以前，从 `INFORMATION_SCHEMA` 选择的结果集中的列头使用查询中指定的大小写，例
    如这个查询 `SELECT table_name FROM INFORMATION_SCHEMA.TABLES;` 结果集的列头
    是 `table_name`，从 MySQL 8.0 起，列头全被大写，所以前面的查询结果集的列头就
    变为 `TABLE_NAME`。如果必要的话，可以使用列别名来实现不同的字母大小写。例如
    ：`SELECT table_name AS 'table_name' FROM INFORMATION_SCHEMA.TABLES`;

* 数据目录影响 `mysqldump` 和 `mysqlpump` 怎样从系统数据库 `mysql` 中转储信息：

  * 以前，可以把系统数据库 `mysql` 中的所有表都转储，从 MySQL 起，`mysqldump` 和
    `mysqlpump` 只转储 `mysql` 中的非数据字典表。

  * Previously, the `--routines` and `--events` options were not required to include stored routines and events when using the `--all-databases` option: The dump included the `mysql` system database, and therefore also the `proc` and `event` tables containing stored routine and event definitions. As of MySQL 8.0, the `event` and `proc` tables are not used. Definitions for the corresponding objects are stored in data dictionary tables, but those tables are not dumped. To include stored routines and events in a dump made using `--all-databases`, use the `--routines` and `--events` options explicitly.
  * 在此之前，`--routines` 和 `--events` 选项在使用 `--all-databases` 选项时不需要包含存储的例程和事件：转储包括 `mysql` 系统数据库，因此也包括包含存储例程和事件定义的 `proc` 和 `events` 表。在 MySQL 8.0 中，`events` 和 `proc` 表没有被使用。对应对象的定义存储在数据字典表中，但是这些表没有被转储。为了将存储的例程和事件包含在一个使用 `--all-databases` 的转储中，可以显式地使用 `--routines` 和 `--events` 选项。

  * Previously, the `--routines` option required the `SELECT` privilege for the `proc` table. As of MySQL 8.0, that table is not used; `--routines` requires the global `SELECT` privilege instead.
  * 在此之前，`--routines` 选项需要 `proc` 表的 `SELECT` 特权。在 MySQL 8.0 中，该表没有使用;`--routines` 需要全局 `SELECT` 特权。

  * Previously, it was possible to dump stored routine and event definitions together with their creation and modification timestamps, by dumping the `proc` and `event` tables. As of MySQL 8.0, those tables are not used, so it is not possible to dump timestamps.
  * 在此之前，可以通过转储 `proc` 和 `event` 表，将存储的例程和事件定义连同它们的创建和修改时间戳一起转储。在 MySQL 8.0 中，这些表没有被使用，因此不可能转储时间戳。

* 以前，创建包含非法字符的存储例程产生一个警告。在 MySQL 8.0 中，则产生一个错误。

## 14.8 Data Dictionary Limitations 


本节描述了 MySQL 数据字典引入的临时限制。

* 在数据目录下手动创建数据库目录（例如，使用 `mkdir`）是不支持的。手动创建的数据
  库目录不被 MySQL 服务器识别。

* DDL 操作需要更长的时间，因为要写入存储器、撤消日志和重做日志，而不是 `.frm` 文件。
* DDL operations take longer due to writing to storage, undo logs, and redo logs instead of `.frm` files.

