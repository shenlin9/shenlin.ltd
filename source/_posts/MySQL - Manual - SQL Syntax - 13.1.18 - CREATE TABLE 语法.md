---
title: MySQL - Manual - SQL Syntax - 13.1.18 - CREATE TABLE 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - CREATE TABLE
---

MySQL `CREATE TABLE` 语法

<!--more-->

```
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    (create_definition,...)
    [table_options]
    [partition_options]

CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    [(create_definition,...)]
    [table_options]
    [partition_options]
    [IGNORE | REPLACE]
    [AS] query_expression

CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    { LIKE old_tbl_name | (LIKE old_tbl_name) }

create_definition:
    col_name column_definition
    | [CONSTRAINT [symbol]] PRIMARY KEY [index_type] (key_part,...)
        [index_option] ...
    | {INDEX|KEY} [index_name] [index_type] (key_part,...)
        [index_option] ...
    | [CONSTRAINT [symbol]] UNIQUE [INDEX|KEY]
        [index_name] [index_type] (key_part,...)
        [index_option] ...
    | {FULLTEXT|SPATIAL} [INDEX|KEY] [index_name] (key_part,...)
        [index_option] ...
    | [CONSTRAINT [symbol]] FOREIGN KEY
        [index_name] (col_name,...) reference_definition
    | CHECK (expr)

column_definition:
    data_type [NOT NULL | NULL] [DEFAULT {literal | (expr)}]
        [AUTO_INCREMENT] [UNIQUE [KEY]] [[PRIMARY] KEY]
        [COMMENT 'string']
        [COLUMN_FORMAT {FIXED|DYNAMIC|DEFAULT}]
        [reference_definition]
    | data_type [GENERATED ALWAYS] AS (expression)
        [VIRTUAL | STORED] [NOT NULL | NULL]
        [UNIQUE [KEY]] [[PRIMARY] KEY]
        [COMMENT 'string']

data_type:
    (see Chapter 11, Data Types)

key_part: {col_name [(length)] | (expr)} [ASC | DESC]

index_type:
    USING {BTREE | HASH}

index_option:
    KEY_BLOCK_SIZE [=] value
    | index_type
    | WITH PARSER parser_name
    | COMMENT 'string'
    | {VISIBLE | INVISIBLE}

reference_definition:
    REFERENCES tbl_name (key_part,...)
        [MATCH FULL | MATCH PARTIAL | MATCH SIMPLE]
        [ON DELETE reference_option]
        [ON UPDATE reference_option]

reference_option:
    RESTRICT | CASCADE | SET NULL | NO ACTION | SET DEFAULT

table_options:
    table_option [[,] table_option] ...

table_option:
    AUTO_INCREMENT [=] value
    | AVG_ROW_LENGTH [=] value
    | [DEFAULT] CHARACTER SET [=] charset_name
    | CHECKSUM [=] {0 | 1}
    | [DEFAULT] COLLATE [=] collation_name
    | COMMENT [=] 'string'
    | COMPRESSION [=] {'ZLIB'|'LZ4'|'NONE'}
    | CONNECTION [=] 'connect_string'
    | {DATA|INDEX} DIRECTORY [=] 'absolute path to directory'
    | DELAY_KEY_WRITE [=] {0 | 1}
    | ENCRYPTION [=] {'Y' | 'N'}
    | ENGINE [=] engine_name
    | INSERT_METHOD [=] { NO | FIRST | LAST }
    | KEY_BLOCK_SIZE [=] value
    | MAX_ROWS [=] value
    | MIN_ROWS [=] value
    | PACK_KEYS [=] {0 | 1 | DEFAULT}
    | PASSWORD [=] 'string'
    | ROW_FORMAT [=] {DEFAULT|DYNAMIC|FIXED|COMPRESSED|REDUNDANT|COMPACT}
    | STATS_AUTO_RECALC [=] {DEFAULT|0|1}
    | STATS_PERSISTENT [=] {DEFAULT|0|1}
    | STATS_SAMPLE_PAGES [=] value
    | TABLESPACE tablespace_name
    | UNION [=] (tbl_name[,tbl_name]...)

partition_options:
    PARTITION BY
        { [LINEAR] HASH(expr)
        | [LINEAR] KEY [ALGORITHM={1|2}] (column_list)
        | RANGE{(expr) | COLUMNS(column_list)}
        | LIST{(expr) | COLUMNS(column_list)} }
    [PARTITIONS num]
    [SUBPARTITION BY
        { [LINEAR] HASH(expr)
        | [LINEAR] KEY [ALGORITHM={1|2}] (column_list) }
      [SUBPARTITIONS num]
    ]
    [(partition_definition [, partition_definition] ...)]

partition_definition:
    PARTITION partition_name
    [VALUES
        {LESS THAN {(expr | value_list) | MAXVALUE}
        |
        IN (value_list)}]
    [[STORAGE] ENGINE [=] engine_name]
    [COMMENT [=] 'string' ]
    [DATA DIRECTORY [=] 'data_dir']
    [INDEX DIRECTORY [=] 'index_dir']
    [MAX_ROWS [=] max_number_of_rows]
    [MIN_ROWS [=] min_number_of_rows]
    [TABLESPACE [=] tablespace_name]
    [(subpartition_definition [, subpartition_definition] ...)]

subpartition_definition:
    SUBPARTITION logical_name
        [[STORAGE] ENGINE [=] engine_name]
        [COMMENT [=] 'string' ]
        [DATA DIRECTORY [=] 'data_dir']
        [INDEX DIRECTORY [=] 'index_dir']
        [MAX_ROWS [=] max_number_of_rows]
        [MIN_ROWS [=] min_number_of_rows]
        [TABLESPACE [=] tablespace_name]

query_expression:
    SELECT ... (Some valid select or union statement)
```

`CREATE TABLE` 使用指定的名字创建一个表，需要你必须有表的 `CREATE` 特权。

默认情况下，表是使用 InnoDB 存储引擎在默认数据库中创建的。如果要创建的表已存在，
或者没有设置默认数据库，或者默认数据库不存在，都会发生错误。

有关表的物理表示的信息，参考 Section 13.1.18.2,“Files Created by CREATE TABLE”

原始的 `CREATE TABLE` 语句，包括所有规范和表选项，在创建表时由 MySQL 存储。更多
信息参考 Section 13.1.18.1, “CREATE TABLE Statement Retention”.

`CREATE TABLE` 语句有几个方面，在本节的主题中描述如下：
* Table Name
* Temporary Tables
* Cloning or Copying a Table
* Column Data Types and Attributes
* Indexes and Foreign Keys
* Table Options
* Creating Partitioned Tables

## Table Name

* tbl_name

表名可以指定为 `db_name.tbl_name` 以在特定的数据库中创建表。这种方法假设数据库存
在，无论是否存在默认数据库都有效。如果使用引号标识符，请分别引用数据库和表名。例
如，写 ``mydb``.``mytbl``，而不是 `mydb.mytbl`。

允许的表名规则参考 Section 9.2, “Schema Object Names”.

* IF NOT EXISTS

防止在表已存在的情况下发生错误。但是，没有验证现有的表与 `CREATE TABLE` 语句所表
明的表是否具有相同的结构。

## Temporary Tables

创建表时可以使用 `TEMPORARY` 关键字，`TEMPORARY` 表只对当前的会话可见，并且会在
会话关闭后被自动删除。更多信息参考 Section 13.1.18.3, “CREATE TEMPORARY TABLE
Syntax”.

## Cloning or Copying a Table

* LIKE

使用 `CREATE TABLE ... LIKE` 根据另一个表的定义创建一个空表，包括原表中定义的任
何列属性和索引:
```
CREATE TABLE new_tbl LIKE orig_tbl;
```
更多信息参考 Section 13.1.18.4, “CREATE TABLE ... LIKE Syntax”.

* [AS] query_expression

要从一个表创建另一个表，在 `CREATE TABLE` 语句的末尾添加一个 `SELECT` 语句：
```
CREATE TABLE new_tbl AS SELECT * FROM orig_tbl;
```
更多信息参考 Section 13.1.18.5, “CREATE TABLE ... SELECT Syntax”.

* IGNORE|REPLACE

`IGNORE` 和 `REPLACE` 选项指示在使用 `SELECT` 语句复制表时如何处理重复的唯一键值
的行。更多信息参考 Section 13.1.18.5, “CREATE TABLE ... SELECT Syntax”.

## Column Data Types and Attributes

每个表有 4096 列的硬限制，但是对于给定的表，有效的最大限制可能更小，这取决于
C.10.4 节中讨论的因素，“Limits on Table Column Count and Row Size”。

### data_type

`data_type` 表示列定义中的数据类型。有关指定的列数据类型可用语法的完整描述，以及
关于每种类型的属性的信息，请参见 Chapter 11, Data Types。

* 有些属性并不适用于所有数据类型。`AUTO_INCREMENT` 仅适用于整数和浮点类型。在
  MySQL 8.0.13 之前，`DEFAULT` 不适用于 `BLOB`、`TEXT`、`GEOMETRY` 和 `JSON` 类
  型。

* 字符数据类型(`CHAR`, `VARCHAR`, `TEXT`) 可以包含 `CHARACTER SET` 和 `COLLATE`
  属性来为列指定字符集和校对规则，更多信息参考 Chapter 10, Character Sets,
  Collations, Unicode。`CHARSET` 是 `CHARACTER SET` 的同义词。例如：
  ```
  CREATE TABLE t (c CHAR(20) CHARACTER SET utf8 COLLATE utf8_bin);
  ```
  MySQL 8.0 对于列定义中长度规范的解释，字符列按字符数计算，`BINARY` 和
  `VARBINARY` 列按字节数计算。

  * 对于 `CHAR`、`VARCHAR`、`BINARY` 和 `VARBINARY` 列，只可以使用列值的开头部分
    创建索引，使用 `col_name(length)` 语法指定索引前缀长度。 `BLOB` 和 `TEXT` 列
    也可以建立索引，但是必须给出前缀长度。非二进制字符串类型的前缀长度以字符数表
    示，二进制字符串类型的前缀长度以字节数表示。也就是说，`CHAR`、`VARCHAR` 和
    `TEXT` 列的索引条目由每个列值的第一个长度字符组成，`BINARY`、`VARBINARY` 和
    `BLOB` 列的索引条目由每个列值的第一个长度字节组成。

    像这样只索引列值的前缀可以使索引文件更小。有关索引前缀的更多信息，请参见
    Section 13.1.14, “CREATE INDEX Syntax”。

    只有 `InnoDB` 和 `MyISAM` 存储引擎支持在 `BLOB` 和 `TEXT` 列上索引，例如：
    ```
    CREATE TABLE test (blob_col BLOB, INDEX(blob_col(10)));
    ```
    如果指定的索引前缀超过了列数据类型最大大小，`CREATE TABLE` 将按照以下方式处
    理索引：
    * 对于非唯一索引，如果启用了严格 SQL 模式则发生错误，如果没有启用严格 SQL 模
      式，则会将索引长度减少到列数据类型最大大小，并生成警告。
    * 对于唯一索引，无论 SQL 模式如何，都会发生错误，因为减少索引长度可能会插入
      不满足指定唯一性要求的非唯一条目。

  * JSON 列不能被索引。您可以通过在生成的列上创建索引来解决这个限制，该生成的列
    是从 JSON 列中提取标量值。有关详细示例，请参见下面章节的 Indexing a
    Generated Column to Provide a JSON Column Index。

### NOT NULL | NULL

如果没有指定 `NULL` 或 `NOT NULL` ，则列将被视为指定了 `NULL` 。在 MySQL 8.0 中
，只有 `InnoDB`、`MyISAM` 和 `MEMORY` 存储引擎支持索引包含 `NULL` 值的列。在其他
情况下，必须声明索引列为 `NOT NULL` 否则将出现错误结果。

### DEFAULT

为一个列指定默认值。有关默认值处理的更多信息，包括列定义中不包含显式的 `DEFAULT`
值的情况，请参见 Section 11.7, “Data Type Default Values”。

如果启用了 `NO_ZERO_DATE` 或 `NO_ZERO_IN_DATE` SQL 模式，并且根据该模式日期默认
值不正确，那么 `CREATE TABLE` 将在严格 SQL 模式未启用下生成警告，在严格模式启用
下生成错误。例如，在启用 `NO_ZERO_IN_DATE` 的情况下， `c1 DATE DEFAULT
2010-00-00` 将产生一个警告。

### AUTO_INCREMENT

整数或浮点列可以具有附加属性 `AUTO_INCREMENT` 。当您将 NULL(推荐值)或 0 插入索引
的 `AUTO_INCREMENT` 列时，该列将被设置为下一个序列值。通常这是 `value+1` ，其中
`value` 是表中当前列的最大值。 `AUTO_INCREMENT` 序列以 1 开头。

要在插入行之后检索 `AUTO_INCREMENT` 值，可以使用 `LAST_INSERT_ID()` SQL 函数或
`mysql_insert_id()` C API 函数。参见 Section 12.14, “Information Functions” 和
Section 27.7.7.38, “mysql_insert_id()”。

如果启用了 `NO_AUTO_VALUE_ON_ZERO` SQL 模式，则可以 将 0 实际的存储到
`AUTO_INCREMENT` 列中而不生成新的序列值。参见 Section 5.1.10, “Server SQL Modes
”。

每个表只能有一个 `AUTO_INCREMENT` 列，它必须被索引，并且不能有 `DEFAULT` 值。只
有当 `AUTO_INCREMENT` 列只包含正数时，它才能正常工作。插入一个负数被认为是插入一
个非常大的正数。这样做是为了避免在数字从正数 “换行” 到负数时出现精度问题，也是
为了确保您不会意外地得到包含 0 值的 `AUTO_INCREMENT` 列。

For MyISAM tables, you can specify an `AUTO_INCREMENT` secondary column in a
multiple-column key.
对于 MyISAM 表，可以在多列键中指定 `AUTO_INCREMENT` 辅助列。参见 Section 3.6.9,
“Using AUTO_INCREMENT”。

要使 MySQL 与某些 ODBC 应用程序兼容，可以使用以下查询为最后插入的行找到
`AUTO_INCREMENT` 值：
```
SELECT * FROM tbl_name WHERE auto_col IS NULL
```
这个方法要求变量 `sql_auto_is_null` 没有被设置为 0，参考 Section 5.1.7, “Server
System Variables”.

关于 InnoDB 和 `AUTO_INCREMENT` 的信息参考 Section 15.8.1.5, “AUTO_INCREMENT
Handling in InnoDB”

关于 `AUTO_INCREMENT` 和 MySQL 复制的信息参考 Section 17.4.1.1, “Replication
and AUTO_INCREMENT”

### COMMENT

可以使用 `COMMENT` 选项指定列的注释，最长可达 1024 个字符。注释可以通过 `SHOW
CREATE TABLE` 和 `SHOW FULL COLUMNS` 语句显示。

### COLUMN_FORMAT

用于 MySQL 集群以确定列的存储格式。

对使用 NDB 以外的存储引擎的表列，这个选项目前没有影响。

在 MySQL 8.0 及以后版本中，`COLUMN_FORMAT` 将被无声地忽略。

### GENERATED ALWAYS

用于指定生成的列表达式，更多生成列的信息，参考 Section 13.1.18.8, “CREATE TABLE
and Generated Columns”.

存储的生成列可以建立索引。InnoDB 支持虚拟生成列上的二级索引。
参考 Section 13.1.18.9, “Secondary Indexes and Generated Columns”.

## Indexes and Foreign Keys

有几个关键字适用于索引和外键的创建。一般背景除以下描述外，参考 Section 13.1.14,
“CREATE INDEX Syntax”, Section 13.1.18.6, “Using FOREIGN KEY Constraints”.

### CONSTRAINT symbol

如果给定了 `CONSTRAINT symbol` 子句，则 `symbol` 值(如果使用了)在数据库中必须是
唯一的。重复的 `symbol` 会导致错误。

如果没有给出 `CONSTRAINT symbol` 子句，或者 `CONSTRAINT` 关键字后面没有 `symbol`
，则会自动创建约束的名称。

### PRIMARY KEY

唯一索引，其中所有键列必须定义为 `NOT NULL` 。如果没有显式地声明为 `NOT NULL` ，
MySQL 就会隐式地(并且是静默地)声明 `NOT NULL`。一个表只能有一个 `PRIMARY KEY` 。
`PRIMARY KEY` 的名称总是 `PRIMARY` ，因此不能用作任何其他类型索引的名称。

如果你没有 `PRIMARY KEY`，但应用程序要求你的表中有 `PRIMARY KEY` ，MySQL 返回第
一个没有 `NULL` 列的 `UNIQUE` 索引作为 `PRIMARY KEY`。在 InnoDB 表中，保持
`PRIMARY KEY` 的长度短小以最小化二级索引的存储开销。每个二级索引条目包含对应行的
主键列的副本。(参见 Section 15.8.2.1, “Clustered and Secondary Indexes”)。

在创建的表中，应首先放置 `PRIMARY KEY` ，然后是所有 `UNIQUE` 索引，然后是非唯一
索引。这有助于 MySQL 优化器对要使用的索引排出优先级，并能更快地检测重复的
`UNIQUE` 键。

`PRIMARY KEY` 可以是多列索引。但是，不能直接在某列规范中使用 `PRIMARY KEY` 键属
性创建多列索引。这样做只会将某一列标记为主键。必须使用一个单独的 `PRIMARY
KEY(key_part, ...)` 子句。

如果一个表有 `PRIMARY KEY` 或 `UNIQUE NOT NULL` 索引，且此索引是整数类型的单列，
则可以在 `SELECT` 语句中使用 `_rowid` 引用索引列，如下文 Unique Indexes 中所述。

在 MySQL 中，`PRIMARY KEY` 的名称是 `PRIMARY`。对于其他索引，如果不指定名称，则
将索引名称指定为与第一个索引列相同的名称，并使用可选的后缀(`_2, _3, ...`)使其唯
一。您可以使用 `SHOW INDEX FROM tbl_name` 查看表的索引名。参见 Section
13.7.6.22, “SHOW INDEX Syntax”。

### KEY | INDEX

`KEY` 通常是 `INDEX` 的同义词。在列定义中键属性 `PRIMARY KEY` 也可以就指定为
`KEY`。这是为了与其他数据库系统兼容而实现的。

### UNIQUE

`UNIQUE` 索引创建一个约束，使索引中的所有值必须是不同的。如果试图添加匹配现有行
键值的新行，则会发生错误。对于所有引擎，`UNIQUE` 索引允许包含 `NULL` 的列具有多
个 `NULL` 值。如果在 `UNIQUE` 索引中为列指定前缀值，则列值在前缀长度内也必须唯一
。

如果一个表有 `PRIMARY KEY` 或 `UNIQUE NOT NULL` 索引，且此索引是整数类型的单列，
则可以在 `SELECT` 语句中使用 `_rowid` 引用索引列，如下文 Unique Indexes 中所述。

### FULLTEXT

`FULLTEXT` 索引是用于全文搜索的一种特殊类型的索引。

只有 InnoDB 和 MyISAM 存储引擎支持 `FULLTEXT` 索引。它们只能从 `CHAR` 、
`VARCHAR` 和 `TEXT` 列创建。索引总是发生在整个列上；不支持列前缀索引，如果指定，
则忽略任何前缀长度。有关操作的详情，请参阅 Section 12.9,“Full-Text Search
Functions”。

如果全文索引和搜索操作需要特殊处理，可以将 `WITH PARSER` 子句指定为
`index_option` 值，以便将解析器插件与索引关联起来。此子句仅对 `FULLTEXT` 索引有
效。InnoDB 和 MyISAM 支持全文解析器插件。有关更多信息，请参见 28.2.1 Types of
Plugins - Full-Text Parser Plugins 和 Section 28.2.4.4,“Writing Full-Text
Parser Plugins”。

### SPATIAL

您可以在空间数据类型上创建 `SPATIAL` 索引。空间类型只支持 InnoDB 和 MyISAM 表，
索引列必须声明为 `NOT NULL`。参见 Section 11.5, “Spatial Data Types”。

### FOREIGN KEY

MySQL 支持外键(允许跨表交叉引用相关数据)和外键约束(有助于保持广泛的数据一致性)。
有关定义和选项信息，请参见后面的章节 reference_definition 和 reference_option。

使用 InnoDB 存储引擎的分区表不支持外键。
参考 Section 22.6,“Restrictions and Limitations on Partitioning”

### CHECK

`CHECK` 子句被解析，但是被所有存储引擎忽略。

参考 Section 1.8.2.3, “Foreign Key Differences”.

### key_part

* `key_part` 规范可以以 `ASC` 或 `DESC` 结束，以指定索引值是按升序存储还是按降序
  存储。如果没有给出顺序说明符，则默认值为升序。

* 前缀由 `length` 属性定义，对于使用 `REDUNDANT` 或 `COMPACT` 行格式的 InnoDB 表
  ，前缀可以长达 767 字节。对于使用 `DYNAMIC` 或 `COMPRESSED` 行格式的 InnoDB 表
  ，前缀长度限制为 3072 字节。对于 MyISAM 表，前缀长度限制是 1000 字节。前缀限制
  以字节为单位度量。但是，`CREATE TABLE`、`ALTER TABLE` 和 `CREATE INDEX` 语句中
  索引规范的前缀长度被解释为非二进制字符串类型(`CHAR`、`VARCHAR`、`TEXT`)的字符
  数，以及二进制字符串类型(`BINARY`、`VARBINARY`、`BLOB`)的字节数。在为使用多字
  节字符集的非二进制字符串列指定前缀长度时，请考虑这一点。

### index_type

一些存储引擎允许您在创建索引时指定索引类型。

`index_type` 说明符的语法是 `USING type_name`。

例如：
```
CREATE TABLE lookup
(id INT, INDEX USING BTREE (id))
ENGINE = MEMORY;
```

`USING` 的首选位置是在索引列列表之后。它可以在列列表之前给出，但是不赞成在该位置
使用该选项，并将在未来的 MySQL 版本中删除。

### index_option

`index_option` 值为索引指定其他选项。

* `KEY_BLOCK_SIZE`

对于 MyISAM 表，`KEY_BLOCK_SIZE` 可选地指定了索引键块使用的字节大小。该值被视为
提示；如果需要，可以使用不同的大小。为单个索引定义指定的 `KEY_BLOCK_SIZE` 值会覆
盖表级的 `KEY_BLOCK_SIZE` 值。有关表级 `KEY_BLOCK_SIZE` 属性的信息，请参见 Table
Options。

* `WITH PARSER`

`WITH PARSER` 选项只能用于 `FULLTEXT` 索引。如果全文索引和搜索操作需要特殊处理，
它将解析器插件与索引关联起来。InnoDB 和 MyISAM 支持全文解析器插件。如果您有一个
MyISAM 表和一个相关的全文解析器插件，那么您可以使用 `ALTER TABLE` 将该表转换为
InnoDB。

* `COMMENT`

在 MySQL 8.0 中，索引定义可以包含一个可选注释，最多 1024 个字符。您可以使用
`index_option COMMENT` 子句为单个索引设置 `InnoDB MERGE_THRESHOLD` 值。参见
Section 15.6.12, “Configuring the Merge Threshold for Index Pages”。有关允许的
`index_option` 值的更多信息，请参见 Section 13.1.14, “CREATE INDEX Syntax”。有
关索引的更多信息，请参见 Section 8.3.1, “How MySQL Uses Indexes”。

### reference_definition

有关 `reference_definition` 语法细节和示例，请参见 Section 13.1.18.6, “Using
FOREIGN KEY Constraints”。有关 InnoDB 中特定于外键的信息，请参见 Section
15.8.1.6, “InnoDB and FOREIGN KEY Constraints”。

InnoDB 表支持检查外键约束。引用表的列必须总是显式指定的，包括外键上的 `ON
DELETE` 和 `ON UPDATE` 操作。有关更详细的信息和示例，请参见 Section 13.1.18.6,
“Using FOREIGN KEY Constraints”。有关 InnoDB 中特定于外键的信息，请参见
Section 15.8.1.6, “InnoDB and FOREIGN KEY Constraints”。

对于其他存储引擎，MySQL 服务器解析并忽略 `CREATE TABLE` 语句中的 `FOREIGN KEY`
和 `REFERENCES` 语法。参见 Section 1.8.2.3, “Foreign Key Differences”。

#### 重要

对于熟悉 ANSI/ISO SQL 标准的用户，请注意，没有存储引擎(包括 InnoDB)可以识别或强
制引用完整性约束定义（referential integrity constraint definitions）中使用的
`MATCH` 子句。使用显式的 `MATCH` 子句不会产生指定的效果，还会导致 `ON DELETE` 和
`ON UPDATE` 子句被忽略。由于这些原因，应该避免指定 `MATCH` 子句。

SQL 标准中的 `MATCH` 子句控制组合(多列)外键中的 `NULL` 值在与主键比较时如何被处
理。InnoDB 本质上实现了由 `MATCH SIMPLE` 定义的语义，它允许外键全部或部分为
`NULL` 。在这种情况下，包含此类外键的(子表)行允许插入，并且不匹配引用的(父)表中
的任何行。使用触发器实现其他语义是可能的。

此外，MySQL 要求对引用的列进行索引以提高性能。然而，InnoDB 并没有强制要求引用的
列声明为 `UNIQUE` 或 `NOT NULL` 。如果外键引用了非唯一键或包含 `NULL` 值的键，此
类外键对于涉及 `UPDATE` 或 `DELETE CASCADE` 之类的操作都没有很好的定义。因此建议
您使用外键时，只引用同时是 `UNIQUE`(或 `PRIMARY`)和 `NOT NULL` 的外键。

MySQL 解析但忽略行内引用规范（“inline REFERENCES specifications”，如 SQL 标准
中定义的，将引用定义为列规范的一部分)。只有 `REFERENCES` 子句被指定为单独的
`FOREIGN KEY` 规范的一部分时 MySQL 才接受 `REFERENCES` 子句。

### reference_option

关于选项 `RESTRICT`, `CASCADE`, `SET NULL`, `NO ACTION`, `SET DEFAULT` 的信息参
考 Section 13.1.18.6, “Using FOREIGN KEY Constraints”。

## Table Options

Options that do not apply to a given storage engine may be accepted and
remembered as part of the table definition.
表选项用于优化表的行为。在大多数情况下，您不必指定它们中的任何一个。除非另有说明
，否则这些选项适用于所有存储引擎。不适用于给定存储引擎的选项可以作为表定义的一部
分被接受和记住。如果您稍后使用 `ALTER TABLE` 将表转换为使用不同的存储引擎，则可
以应用这些选项。

* ENGINE

使用下表中显示的名称之一指定表的存储引擎。引擎名可以是非引用的，也可以是使用引号
引用的。引用的名称 `'DEFAULT'` 可以被识别但会被忽略。

    Storage Engine          Description
    -------------------------------------
    InnoDB
                            具有行锁和外键的事务安全表。是新表的默认存储引擎。参
                            考 Chapter 15, The InnoDB Storage Engine
    MyISAM
                            二进制可移植存储引擎，主要用于只读或大部分是读取的工
                            作负载。参考 Section 16.2, “The MyISAM Storage
                            Engine”.
    MEMORY
                            此存储引擎的数据仅存储在内存中，参考 Section 16.3,
                            “The MEMORY Storage Engine”.
    CSV
                            以逗号分隔值格式存储行的表。参考 Section 16.4, “The
                            CSV Storage Engine”.
    ARCHIVE
                            存档存储引擎，参考 Section 16.5, “The ARCHIVE
                            Storage Engine”.
    EXAMPLE
                            一个示例存储引擎，参考 Section 16.9, “The EXAMPLE
                            Storage Engine”.
    FEDERATED
                            访问远程表的存储引擎。参考 Section 16.8, “The
                            FEDERATED Storage Engine”.
    HEAP                    
                            MEMORY 的同义词
    MERGE
                            多个 MyISAM 表的集合，作为一个表使用，也称为
                            MRG_MyISAM，参考 Section 16.7, “The MERGE Storage
                            Engine”.

默认情况下，如果指定了不可用的存储引擎，则语句执行失败并提示错误。您可以通过从服
务器 SQL 模式中删除 `NO_ENGINE_SUBSTITUTION` (请参阅 Section 5.1.10, “Server
SQL Modes”)来覆盖此行为，以便 MySQL 允许使用默认存储引擎替换指定的引擎。通常在
这种情况下，就是 InnoDB，它是系统变量 `default_storage_engine` 的默认值。当禁用
`NO_ENGINE_SUBSTITUTION` 时，如果不遵守存储引擎规范，就会出现警告。

* AUTO_INCREMENT

表的初始 `AUTO_INCREMENT` 值。在 MySQL 8.0 中，这适用于 `MyISAM`、`MEMORY`、
`InnoDB` 和 `ARCHIVE` 表。若要为不支持 `AUTO_INCREMENT` 表选项的引擎设置第一个自
动递增值，请在创建表后插入一个值小于所需值的 “虚假” 行，然后删除虚假行(dummy
row)。

对于在 `CREATE TABLE` 语句中支持 `AUTO_INCREMENT` 表选项的引擎，还可以使用
`ALTER table tbl_name AUTO_INCREMENT = N` 重置 `AUTO_INCREMENT` 值。不能将该值设
置为比列中当前最大值还要小。

* AVG_ROW_LENGTH

表示表的平均行长度的近似值。您只需要为具有可变大小行的大型表设置此设置。

When you create a MyISAM table, MySQL uses the product of the `MAX_ROWS` and `AVG_ROW_LENGTH` options to decide how big the resulting table is. If you don't specify either option, the maximum size for `MyISAM` data and index files is 256TB by default. (If your operating system does not support files that large, table sizes are constrained by the file size limit.) If you want to keep down the pointer sizes to make the index smaller and faster and you don't really need big files, you can decrease the default pointer size by setting the `myisam_data_pointer_size` system variable. (See Section 5.1.7, “Server System Variables”.) If you want all your tables to be able to grow above the default limit and are willing to have your tables slightly slower and larger than necessary, you can increase the default pointer size by setting this variable. Setting the value to 7 permits table sizes up to 65,536TB.
创建 MyISAM 表时，MySQL 使用 `MAX_ROWS` 和 `AVG_ROW_LENGTH` 选项的乘积来决定结果表的大小。如果不指定这两个选项，默认情况下， `MyISAM` 数据和索引文件的最大大小为 256TB。(如果您的操作系统不支持那么大的文件，那么表大小受文件大小限制。)如果您想要降低指针大小以使索引更小、更快，并且实际上并不需要大文件，那么可以通过设置 `myisam_data_pointer_size` 系统变量来减少默认指针大小。（参见 Section 5.1.7, “Server System Variables”）。如果希望所有表都能够增长到默认限制以上，并且希望表稍微慢一些，并且比需要的大一些，那么可以通过设置这个变量来增加默认指针的大小。将值设置为 7，允许表大小为 65,536TB。

* [DEFAULT] CHARACTER SET

Specifies a default character set for the table. `CHARSET` is a synonym for `CHARACTER SET`. If the character set name is `DEFAULT`, the database character set is used.
为表指定默认字符集。 `CHARSET` 是 `CHARACTER SET` 的同义词。如果字符集名称为 `DEFAULT` ，则使用数据库字符集。

* CHECKSUM

Set this to 1 if you want MySQL to maintain a live checksum for all rows (that is, a checksum that MySQL updates automatically as the table changes). This makes the table a little slower to update, but also makes it easier to find corrupted tables. The `CHECKSUM TABLE` statement reports the checksum.  (MyISAM only.)
如果希望 MySQL 为所有行维护一个活动校验和(即，MySQL 在表更改时自动更新的校验和)，请将其设置为1。这使表更新稍微慢一些，但也使查找损坏的表变得更容易。`CHECKSUM TABLE`语句报告校验和。(只 MyISAM。)

* [DEFAULT] COLLATE

为表指定默认校对规则。

* COMMENT

A comment for the table, up to 2048 characters long.

You can set the InnoDB `MERGE_THRESHOLD` value for a table using the table_option `COMMENT` clause. See Section 15.6.12, “Configuring the Merge Threshold for Index Pages”.
您可以使用table_option ' COMMENT '子句为表设置InnoDB ' MERGE_THRESHOLD '值。参见15.6.12节“为索引页配置合并阈值”。

* COMPRESSION

The compression algorithm used for page level compression for InnoDB tables. Supported values include `Zlib`, `LZ4`, and None. The `COMPRESSION` attribute was introduced with the transparent page compression feature. Page compression is only supported with InnoDB tables that reside in file-per- table tablespaces, and is only available on Linux and Windows platforms that support sparse files and hole punching. For more information, see Section 15.9.2, “InnoDB Page Compression”.
用于 InnoDB 表页级压缩的压缩算法。支持的值包括 `Zlib` 、 `LZ4` 和 `None`。 `COMPRESSION` 属性引入了透明页面压缩特性。页面压缩只支持位于每个表空间文件中的 InnoDB 表，并且只在支持稀疏文件和穿孔的 Linux 和 Windows 平台上可用。有关更多信息，请参见 Section 15.9.2, “InnoDB Page Compression”。

* CONNECTION

The connection string for a `FEDERATED` table.

    注意
    Older versions of MySQL used a `COMMENT` option for the connection string.

* DATA DIRECTORY, INDEX DIRECTORY

For InnoDB, the `DATA DIRECTORY='directory'` option allows you to create InnoDB file-per-table tablespaces outside the MySQL data directory. Within the directory that you specify, MySQL creates a subdirectory corresponding to the database name, and within that a `.ibd` file for the table. The `innodb_file_per_table` configuration option must be enabled to use the `DATA DIRECTORY` option with InnoDB. The full directory path must be specified. See Section 15.7.5, “Creating File-Per-Table Tablespaces Outside the Data Directory” for more information.
对于 InnoDB，`DATA DIRECTORY='directory'` 选项允许您在 MySQL 数据目录之外创建每个表的 InnoDB 文件表空间。在您指定的目录中，MySQL 创建了一个与数据库名称相对应的子目录，在该子目录中有一个 `.ibd` 文件。`innodb_file_per_table` 配置选项必须启用，才能与 InnoDB 一起使用 `DATA DIRECTORY` 选项。必须指定完整的目录路径。有关更多信息，请参见 Section 15.7.5, “Creating File-Per-Table Tablespaces Outside the Data Directory”。

When creating MyISAM tables, you can use the `DATA DIRECTORY='directory'` clause, the `INDEX DIRECTORY='directory'` clause, or both. They specify where to put a MyISAM table's data file and index file, respectively. Unlike InnoDB tables, MySQL does not create subdirectories that correspond to the database name when creating a MyISAM table with a `DATA DIRECTORY` or `INDEX DIRECTORY` option. Files are created in the directory that is specified.
在创建 MyISAM 表时，可以使用 `DATA DIRECTORY='directory'` 子句，`INDEX DIRECTORY='DIRECTORY'` 子句或两者兼而有之。它们分别指定将 MyISAM 表的数据文件和索引文件放置在何处。与 InnoDB 表不同，MySQL 在创建带有 `DATA DIRECTORY` 或 `INDEX DIRECTORY` 选项的 MyISAM 表时，不会创建与数据库名称对应的子目录。文件是在指定的目录中创建的。

要使用 `DATA DIRECTORY` 或 `INDEX DIRECTORY` 表选项，必须有 `FILE` 特权。

    重要

    表级选项 `DATA DIRECTORY` 和 `INDEX DIRECTORY` 会被分区表忽略. (Bug #32091)

These options work only when you are not using the `--skip-symbolic-links` option. Your operating system must also have a working, thread-safe `realpath()` call. See Section 8.12.2.2, “Using Symbolic Links for MyISAM Tables on Unix”, for more complete information.
这些选项只有在您不使用 `--skip-symbolic-links` 选项时才有效。您的操作系统还必须有一个工作的、线程安全的 `realpath()` 调用。有关更完整的信息，请参见 Section 8.12.2.2, “Using Symbolic Links for MyISAM Tables on Unix”。

If a MyISAM table is created with no `DATA DIRECTORY` option, the `.MYD` file is created in the database directory. By default, if MyISAM finds an existing `.MYD` file in this case, it overwrites it. The same applies to `.MYI` files for tables created with no `INDEX DIRECTORY` option. To suppress this behavior, start the server with the `--keep_files_on_create` option, in which case MyISAM will not overwrite existing files and returns an error instead.
如果创建 MyISAM 表时没使用 `DATA DIRECTORY` 选项，则`.MYD` 文件是在数据库目录中创建的。这种情况下如果 MyISAM 找到一个已存在的 `.MYD` 文件，则默认覆盖它。这同样适用于表创建时没使用 `INDEX DIRECTORY` 选项的 `.MYI` 文件。要抑制这种行为，可以启动服务器时使用 `--keep_files_on_create` 选项，在这种情况下，MyISAM 不会覆盖已存在文件，而是返回一个错误。

If a MyISAM table is created with a `DATA DIRECTORY` or `INDEX DIRECTORY` option and an existing `.MYD` or `.MYI` file is found, MyISAM always returns an error. It will not overwrite a file in the specified directory.
如果创建 MyISAM 表时使用了 `DATA DIRECTORY` 或 `INDEX DIRECTORY` 选项且又发现了已存在的 `.MYD` 或 `.MYI` 文件，则 MyISAM 总是返回错误。它不会覆盖指定目录中的文件。

    重要
    不能使用包含 MySQL 数据目录和 `DATA DIRECTORY` 或 `INDEX DIRECTORY` 的路径名。这包括分区表和单个表分区。(参见 Bug #32167)。
    You cannot use path names that contain the MySQL data directory with `DATA DIRECTORY` or `INDEX DIRECTORY`. This includes partitioned tables and individual table partitions. (See Bug #32167.)

* DELAY_KEY_WRITE

Set this to 1 if you want to delay key updates for the table until the table is closed. See the description of the `delay_key_write` system variable in Section 5.1.7, “Server System Variables”. (MyISAM only.)
如果希望将表的键更新延迟到关闭表时，请将此设置为 1。参见 Section 5.1.7, “Server System Variables”中对 `delay_key_write` 系统变量的描述。(只 MyISAM。)

* ENCRYPTION

Set the `ENCRYPTION` option to `'Y'` to enable page-level data encryption for an InnoDB table created in a file-per-table tablespace. Option values are not case-sensitive. The `ENCRYPTION` option was introduced with the InnoDB tablespace encryption feature; see Section 15.7.11, “InnoDB Tablespace Encryption”. The `keyring_file` plugin must be loaded to use the `ENCRYPTION` option.
将 `ENCRYPTION` 选项设置为 `Y` ，以便为在每个表空间文件中创建的InnoDB表启用页级数据加密。选项值不区分大小写。InnoDB表空间加密功能引入了 `加密` 选项;参见15.7.11节 `InnoDB表空间加密` 。必须加载 `keyring_file` 插件才能使用 `ENCRYPTION` 选项。

* INSERT_METHOD

If you want to insert data into a `MERGE` table, you must specify with `INSERT_METHOD` the table into which the row should be inserted. `INSERT_METHOD` is an option useful for `MERGE` tables only. Use a value of `FIRST` or `LAST` to have inserts go to the first or last table, or a value of `NO` to prevent inserts. See Section 16.7, “The MERGE Storage Engine”.
如果要将数据插入 `MERGE` 表，必须使用 `INSERT_METHOD` 指定应该将行插入的表。`INSERT_METHOD` 是仅对 `MERGE` 表有用的选项。使用 `FIRST` 或 `LAST` 值让插入进入第一个或最后一个表，或者使用 `NO` 值防止插入。参见 Section 16.7, “The MERGE Storage Engine”。

* KEY_BLOCK_SIZE

For MyISAM tables, `KEY_BLOCK_SIZE` optionally specifies the size in bytes to use for index key blocks.  The value is treated as a hint; a different size could be used if necessary. A `KEY_BLOCK_SIZE` value specified for an individual index definition overrides the table-level `KEY_BLOCK_SIZE` value.
对于 MyISAM 表，`KEY_BLOCK_SIZE` 可选地指定索引键块使用的字节大小。该值被视为提示;如果需要，可以使用不同的大小。为单个索引定义指定的 `KEY_BLOCK_SIZE` 值覆盖表级的 `KEY_BLOCK_SIZE` 值。

For InnoDB tables, `KEY_BLOCK_SIZE` optionally specifies the page size (in kilobytes) to use for compressed InnoDB tables. The `KEY_BLOCK_SIZE` value is treated as a hint; a different size could be used by InnoDB if necessary. `KEY_BLOCK_SIZE` can only be less than or equal to the `innodb_page_size` value. A value of 0 represents the default compressed page size, which is half of the `innodb_page_size` value. Depending on `innodb_page_size`, possible `KEY_BLOCK_SIZE` values include 0, 1, 2, 4, 8, and 16. See Section 15.9.1, “InnoDB Table Compression” for more information.
对于InnoDB表， `KEY_BLOCK_SIZE` 可选地指定用于压缩InnoDB表的页面大小(单位为千字节)。 `KEY_BLOCK_SIZE` 值被视为提示;如果需要，InnoDB可以使用不同的大小。 `KEY_BLOCK_SIZE` 只能小于或等于 `innodb_page_size` 的值。值0表示默认的压缩页面大小，是 `innodb_page_size` 值的一半。根据 `innodb_page_size` ，可能的 `KEY_BLOCK_SIZE` 值包括0、1、2、4、8和16。有关更多信息，请参见15.9.1节 `InnoDB表压缩` 。

Oracle recommends enabling `innodb_strict_mode` when specifying `KEY_BLOCK_SIZE` for InnoDB tables. When `innodb_strict_mode` is enabled, specifying an invalid `KEY_BLOCK_SIZE` value returns an error. If `innodb_strict_mode` is disabled, an invalid `KEY_BLOCK_SIZE` value results in a warning, and the `KEY_BLOCK_SIZE` option is ignored.
Oracle 建议在为 InnoDB 表指定 `KEY_BLOCK_SIZE` 时启用 `innodb_strict_mode` 。当 `innodb_strict_mode` 被启用时，指定一个无效的 `KEY_BLOCK_SIZE` 值将返回一个错误。如果 `innodb_strict_mode` 被禁用，一个无效的 `KEY_BLOCK_SIZE` 值将导致一个警告，并且 `KEY_BLOCK_SIZE` 选项将被忽略。

The `Create_options` column in response to `SHOW TABLE STATUS` reports the actual `KEY_BLOCK_SIZE` used by the table, as does `SHOW CREATE TABLE`.

InnoDB 只在表级支持 `KEY_BLOCK_SIZE`

`KEY_BLOCK_SIZE` is not supported with 32k and 64k `innodb_page_size` values. InnoDB table compression does not support these pages sizes.

InnoDB does not support the `KEY_BLOCK_SIZE` option when creating temporary tables.

* MAX_ROWS

The maximum number of rows you plan to store in the table. This is not a hard limit, but rather a hint to the storage engine that the table must be able to store at least this many rows.  The maximum `MAX_ROWS` value is 4294967295; larger values are truncated to this limit.

* MIN_ROWS

The minimum number of rows you plan to store in the table. The `MEMORY` storage engine uses this option as a hint about memory use.

* PACK_KEYS

Takes effect only with MyISAM tables. Set this option to 1 if you want to have smaller indexes.  This usually makes updates slower and reads faster. Setting the option to 0 disables all packing of keys. Setting it to `DEFAULT` tells the storage engine to pack only long `CHAR`, `VARCHAR`, `BINARY`, or `VARBINARY` columns.

If you do not use `PACK_KEYS`, the default is to pack strings, but not numbers. If you use `PACK_KEYS=1`, numbers are packed as well.

When packing binary number keys, MySQL uses prefix compression:
* Every key needs one extra byte to indicate how many bytes of the previous key are the same for the next key.
* The pointer to the row is stored in `high-byte-first` order directly after the key, to improve compression.

This means that if you have many equal keys on two consecutive rows, all following “same” keys usually only take two bytes (including the pointer to the row). Compare this to the ordinary case where the following keys takes `storage_size_for_key + pointer_size` (where the pointer size is usually 4). Conversely, you get a significant benefit from prefix compression only if you have many numbers that are the same. If all keys are totally different, you use one byte more per key, if the key is not a key that can have `NULL` values. (In this case, the packed key length is stored in the same byte that is used to mark if a key is `NULL`.)

* PASSWORD

此选项未使用。

* ROW_FORMAT

  Defines the physical format in which the rows are stored.

  When executing a `CREATE TABLE` statement with strict mode disabled, if you specify a row format that is not supported by the storage engine that is used for the table, the table is created using that storage engine's default row format. The actual row format of the table is reported in the `Row_format` and `Create_options` columns in response to `SHOW TABLE STATUS`. `SHOW CREATE TABLE` also reports the actual row format of the table.

  Row format choices differ depending on the storage engine used for the table.

  For InnoDB tables:
  * The default row format is defined by `innodb_default_row_format`, which has a default setting of `DYNAMIC`. The default row format is used when the `ROW_FORMAT` option is not defined or when `ROW_FORMAT=DEFAULT` is used.

  If the `ROW_FORMAT` option is not defined, or if `ROW_FORMAT=DEFAULT` is used, operations that rebuild a table also silently change the row format of the table to the default defined by `innodb_default_row_format`. For more information, see Section 15.10.2, “Specifying the Row Format for a Table”.

  * For more efficient InnoDB storage of data types, especially `BLOB` types, use the `DYNAMIC`. See Section 15.10.3, “DYNAMIC and COMPRESSED Row Formats” for requirements associated with the `DYNAMIC` row format.

  * To enable compression for InnoDB tables, specify `ROW_FORMAT=COMPRESSED`. The `ROW_FORMAT=COMPRESSED` option is not supported when creating temporary tables. See Section 15.9, “InnoDB Table and Page Compression” for requirements associated with the `COMPRESSED` row format.

  * The row format used in older versions of MySQL can still be requested by specifying the `REDUNDANT` row format.

  * When you specify a non-default `ROW_FORMAT` clause, consider also enabling the `innodb_strict_mode` configuration option.

  * `ROW_FORMAT=FIXED` is not supported. If `ROW_FORMAT=FIXED` is specified while `innodb_strict_mode` is disabled, InnoDB issues a warning and assumes `ROW_FORMAT=DYNAMIC`.

  If `ROW_FORMAT=FIXED` is specified while `innodb_strict_mode` is enabled, which is the default, InnoDB returns an error.

  * For additional information about InnoDB row formats, see Section 15.10, “InnoDB Row Storage and Row Formats”.

  For MyISAM tables, the option value can be `FIXED` or `DYNAMIC` for static or variable-length row format.  `myisampack` sets the type to `COMPRESSED`. See Section 16.2.3, “MyISAM Table Storage Formats”.

* STATS_AUTO_RECALC

Specifies whether to automatically recalculate persistent statistics for an InnoDB table. The value `DEFAULT` causes the persistent statistics setting for the table to be determined by the `innodb_stats_auto_recalc` configuration option. The value 1 causes statistics to be recalculated when 10% of the data in the table has changed. The value 0 prevents automatic recalculation for this table; with this setting, issue an `ANALYZE TABLE` statement to recalculate the statistics after making substantial changes to the table. For more information about the persistent statistics feature, see Section 15.6.11.1, “Configuring Persistent Optimizer Statistics Parameters”.

* STATS_PERSISTENT

Specifies whether to enable persistent statistics for an InnoDB table. The value `DEFAULT` causes the persistent statistics setting for the table to be determined by the `innodb_stats_persistent` configuration option. The value 1 enables persistent statistics for the table, while the value 0 turns off this feature. After enabling persistent statistics through a `CREATE TABLE` or `ALTER TABLE` statement, issue an `ANALYZE TABLE` statement to calculate the statistics, after loading representative data into the table. For more information about the persistent statistics feature, see Section 15.6.11.1, “Configuring Persistent Optimizer Statistics Parameters”.

* STATS_SAMPLE_PAGES

The number of index pages to sample when estimating cardinality and other statistics for an indexed column, such as those calculated by `ANALYZE TABLE`. For more information, see Section 15.6.11.1,“Configuring Persistent Optimizer Statistics Parameters”.

* TABLESPACE

The `TABLESPACE` option can be used to create a table in an existing general tablespace, a file-per-table tablespace, or the system tablespace.
```
CREATE TABLE tbl_name ... TABLESPACE [=] tablespace_name
```
The general tablespace that you specify must exist prior to using the `TABLESPACE` option. For information about general tablespaces, see Section 15.7.10, “InnoDB General Tablespaces”.

The `tablespace_name` is a case-sensitive identifier. It may be quoted or unquoted. The forward slash character (“/”) is not permitted. Names beginning with “innodb_” are reserved for special use.

To create a table in the system tablespace, specify `innodb_system` as the tablespace name.
```
CREATE TABLE tbl_name ... TABLESPACE [=] innodb_system
```

Using the `TABLESPACE [=] innodb_system` option, you can place a table of any uncompressed row format in the system tablespace regardless of the `innodb_file_per_table` setting. For example, you can add a table with `ROW_FORMAT=DYNAMIC` to the system tablespace using the `TABLESPACE [=] innodb_system` option.

To create a table in a file-per-table tablespace, specify `innodb_file_per_table` as the tablespace name.
```
CREATE TABLE tbl_name ... TABLESPACE [=] innodb_file_per_table
```

    Note
    If `innodb_file_per_table` is enabled, you need not specify
    `TABLESPACE=innodb_file_per_table` to create an InnoDB file-per-table
    tablespace. InnoDB tables are created in file-per-table tablespaces by default
    when `innodb_file_per_table` is enabled.

The `DATA DIRECTORY` clause is permitted with `CREATE TABLE ...`

`TABLESPACE=innodb_file_per_table` but is otherwise not supported for use in combination with the `TABLESPACE` option.

The `TABLESPACE` option is supported with `ALTER TABLE`, which can be used to move tables from one tablespace to another. For more information, see Section 15.7.10, “InnoDB General Tablespaces”.

* UNION

Used to access a collection of identical MyISAM tables as one. This works only with `MERGE` tables. See Section 16.7, “The MERGE Storage Engine”.

You must have `SELECT`, `UPDATE`, and `DELETE` privileges for the tables you map to a `MERGE` table.

    Note
    Formerly, all tables used had to be in the same database as the MERGE table itself. This restriction no longer applies.

## Creating Partitioned Tables

`partition_options` can be used to control partitioning of the table created with `CREATE TABLE`.

Not all options shown in the syntax for `partition_options` at the beginning of this section are available for all partitioning types. Please see the listings for the following individual types for information specific to each type, and see Chapter 22, Partitioning, for more complete information about the workings of and uses for partitioning in MySQL, as well as additional examples of table creation and other statements relating to MySQL partitioning.

Partitions can be modified, merged, added to tables, and dropped from tables. For basic information about the MySQL statements to accomplish these tasks, see Section 13.1.8, “ALTER TABLE Syntax”. For more detailed descriptions and examples, see Section 22.3, “Partition Management”.

* PARTITION BY

If used, a `partition_options` clause begins with `PARTITION BY`. This clause contains the function that is used to determine the partition; the function returns an integer value ranging from 1 to num, where num is the number of partitions. (The maximum number of user-defined partitions which a table may contain is 1024; the number of subpartitions—discussed later in this section—is included in this maximum.)

    Note
    The expression (expr) used in a `PARTITION BY` clause cannot refer to any columns not in the table being created; such references are specifically not permitted and cause the statement to fail with an error. (Bug #29444)

* HASH(expr)

Hashes one or more columns to create a key for placing and locating rows. expr is an expression using one or more table columns. This can be any valid MySQL expression (including MySQL functions) that yields a single integer value. For example, these are both valid `CREATE TABLE` statements using `PARTITION BY HASH`:
```
CREATE TABLE t1 (col1 INT, col2 CHAR(5))
    PARTITION BY HASH(col1);

CREATE TABLE t1 (col1 INT, col2 CHAR(5), col3 DATETIME)
    PARTITION BY HASH ( YEAR(col3) );
```

You may not use either `VALUES LESS THAN` or `VALUES IN` clauses with `PARTITION BY HASH`.

`PARTITION BY HASH` uses the remainder of expr divided by the number of partitions (that is, the modulus). For examples and additional information, see Section 22.2.4, “HASH Partitioning”.

The `LINEAR` keyword entails a somewhat different algorithm. In this case, the number of the partition in which a row is stored is calculated as the result of one or more logical AND operations. For discussion and examples of linear hashing, see Section 22.2.4.1, “LINEAR HASH Partitioning”.

* KEY(column_list)

This is similar to `HASH`, except that MySQL supplies the hashing function so as to guarantee an even data distribution. The `column_list` argument is simply a list of 1 or more table columns (maximum: 16). This example shows a simple table partitioned by key, with 4 partitions:
```
CREATE TABLE tk (col1 INT, col2 CHAR(5), col3 DATE)
PARTITION BY KEY(col3)
PARTITIONS 4;
```

For tables that are partitioned by key, you can employ linear partitioning by using the `LINEAR` keyword.  This has the same effect as with tables that are partitioned by `HASH`. That is, the partition number is found using the `&` operator rather than the modulus (see Section 22.2.4.1, “LINEAR HASH Partitioning”, and Section 22.2.5, “KEY Partitioning”, for details). This example uses linear partitioning by key to distribute data between 5 partitions:
```
CREATE TABLE tk (col1 INT, col2 CHAR(5), col3 DATE)
PARTITION BY LINEAR KEY(col3)
PARTITIONS 5;
```

The `ALGORITHM={1|2}` option is supported with `[SUB]PARTITION BY [LINEAR] KEY`.  `ALGORITHM=1` causes the server to use the same key-hashing functions as MySQL 5.1; `ALGORITHM=2` means that the server employs the key-hashing functions implemented and used by default for new `KEY` partitioned tables in MySQL 5.5 and later. (Partitioned tables created with the key-hashing functions employed in MySQL 5.5 and later cannot be used by a MySQL 5.1 server.) Not specifying the option has the same effect as using `ALGORITHM=2`. This option is intended for use chiefly when upgrading or downgrading `[LINEAR] KEY` partitioned tables between MySQL 5.1 and later MySQL versions, or for creating tables partitioned by `KEY` or `LINEAR KEY` on a MySQL 5.5 or later server which can be used on a MySQL 5.1 server. For more information, see Section 13.1.8.1, “ALTER TABLE Partition Operations”.

`mysqldump` in MySQL 5.7 (and later) writes this option encased in versioned comments, like this:
```
CREATE TABLE t1 (a INT)
/*!50100 PARTITION BY KEY */ /*!50611 ALGORITHM = 1 */ /*!50100 ()
PARTITIONS 3 */
```

This causes MySQL 5.6.10 and earlier servers to ignore the option, which would otherwise cause a syntax error in those versions. If you plan to load a dump made on a MySQL 5.7 server where you use tables that are partitioned or subpartitioned by `KEY` into a MySQL 5.6 server previous to version 5.6.11, be sure to consult Changes Affecting Upgrades to MySQL 5.6, before proceeding. (The information found there also applies if you are loading a dump containing `KEY` partitioned or subpartitioned tables made from a MySQL 5.7—actually 5.6.11 or later—server into a MySQL 5.5.30 or earlier server.)

Also in MySQL 5.6.11 and later, `ALGORITHM=1` is shown when necessary in the output of `SHOW CREATE TABLE` using versioned comments in the same manner as `mysqldump`. `ALGORITHM=2` is always omitted from `SHOW CREATE TABLE` output, even if this option was specified when creating the original table.

You may not use either `VALUES LESS THAN` or `VALUES IN` clauses with `PARTITION BY KEY`.

* RANGE(expr)

In this case, expr shows a range of values using a set of `VALUES LESS THAN` operators. When using range partitioning, you must define at least one partition using `VALUES LESS THAN`. You cannot use `VALUES IN` with range partitioning.

    Note
    For tables partitioned by RANGE, VALUES LESS THAN must be used with either an integer literal value or an expression that evaluates to a single integer value.  In MySQL 8.0, you can overcome this limitation in a table that is defined using PARTITION BY RANGE COLUMNS, as described later in this section.

Suppose that you have a table that you wish to partition on a column containing year values, according to the following scheme.

    Partition Number: Years Range:
    0                   1990 and earlier
    1                   1991 to 1994
    2                   1995 to 1998
    3                   1999 to 2002
    4                   2003 to 2005
    5                   2006 and later

A table implementing such a partitioning scheme can be realized by the `CREATE TABLE` statement shown here:
```
CREATE TABLE t1 (
    year_col INT,
    some_data INT
)
PARTITION BY RANGE (year_col) (
PARTITION p0 VALUES LESS THAN (1991),
PARTITION p1 VALUES LESS THAN (1995),
PARTITION p2 VALUES LESS THAN (1999),
PARTITION p3 VALUES LESS THAN (2002),
PARTITION p4 VALUES LESS THAN (2006),
PARTITION p5 VALUES LESS THAN MAXVALUE
);
```

`PARTITION ... VALUES LESS THAN ...` statements work in a consecutive fashion. `VALUES LESS THAN MAXVALUE` works to specify “leftover” values that are greater than the maximum value otherwise specified.

`VALUES LESS THAN` clauses work sequentially in a manner similar to that of the case portions of a `switch ... case` block (as found in many programming languages such as C, Java, and PHP). That is, the clauses must be arranged in such a way that the upper limit specified in each successive `VALUES LESS THAN` is greater than that of the previous one, with the one referencing `MAXVALUE` coming last of all in the list.

* RANGE COLUMNS(column_list)

This variant on RANGE facilitates partition pruning for queries using range conditions on multiple columns (that is, having conditions such as `WHERE a = 1 AND b < 10 or WHERE a = 1 AND b = 10 AND c < 10`). It enables you to specify value ranges in multiple columns by using a list of columns in the `COLUMNS` clause and a set of column values in each `PARTITION ... VALUES LESS THAN` (`value_list`) partition definition clause. (In the simplest case, this set consists of a single column.) The maximum number of columns that can be referenced in the `column_list` and `value_list` is 16.

The `column_list` used in the `COLUMNS` clause may contain only names of columns; each column in the list must be one of the following MySQL data types: the integer types; the string types; and time or date column types. Columns using `BLOB`, `TEXT`, `SET`, `ENUM`, `BIT`, or spatial data types are not permitted; columns that use floating-point number types are also not permitted. You also may not use functions or arithmetic expressions in the `COLUMNS` clause.

The `VALUES LESS THAN` clause used in a partition definition must specify a literal value for each column that appears in the `COLUMNS()` clause; that is, the list of values used for each `VALUES LESS THAN` clause must contain the same number of values as there are columns listed in the `COLUMNS` clause. An attempt to use more or fewer values in a `VALUES LESS THA`N clause than there are in the `COLUMNS` clause causes the statement to fail with the error Inconsistency in usage of column lists for partitioning.... You cannot use NULL for any value appearing in `VALUES LESS THAN`. It is possible to use `MAXVALUE` more than once for a given column other than the first, as shown in this example:
```
CREATE TABLE rc (
    a INT NOT NULL,
    b INT NOT NULL
)
PARTITION BY RANGE COLUMNS(a,b) (
PARTITION p0 VALUES LESS THAN (10,5),
PARTITION p1 VALUES LESS THAN (20,10),
PARTITION p2 VALUES LESS THAN (50,MAXVALUE),
PARTITION p3 VALUES LESS THAN (65,MAXVALUE),
PARTITION p4 VALUES LESS THAN (MAXVALUE,MAXVALUE)
);
```

Each value used in a `VALUES LESS THAN` value list must match the type of the corresponding column exactly; no conversion is made. For example, you cannot use the string '1' for a value that matches a column that uses an integer type (you must use the numeral 1 instead), nor can you use the numeral 1 for a value that matches a column that uses a string type (in such a case, you must use a quoted string: '1').

For more information, see Section 22.2.1, “RANGE Partitioning”, and Section 22.4, “Partition Pruning”.

* LIST(expr)

This is useful when assigning partitions based on a table column with a restricted set of possible values, such as a state or country code. In such a case, all rows pertaining to a certain state or country can be assigned to a single partition, or a partition can be reserved for a certain set of states or countries. It is similar to `RANGE`, except that only `VALUES IN` may be used to specify permissible values for each partition.

`VALUES IN` is used with a list of values to be matched. For instance, you could create a partitioning scheme such as the following:
```
CREATE TABLE client_firms (
    id INT,
    name VARCHAR(35)
)
PARTITION BY LIST (id) (
PARTITION r0 VALUES IN (1, 5, 9, 13, 17, 21),
PARTITION r1 VALUES IN (2, 6, 10, 14, 18, 22),
PARTITION r2 VALUES IN (3, 7, 11, 15, 19, 23),
PARTITION r3 VALUES IN (4, 8, 12, 16, 20, 24)
);
```
When using list partitioning, you must define at least one partition using VALUES IN. You cannot use `VALUES LESS THAN` with `PARTITION BY LIST`.

    Note
    For tables partitioned by LIST, the value list used with VALUES IN must consist of integer values only. In MySQL 8.0, you can overcome this limitation using partitioning by LIST COLUMNS, which is described later in this section.

* LIST COLUMNS(column_list)

This variant on `LIST` facilitates partition pruning for queries using comparison conditions on multiple columns (that is, having conditions such as `WHERE a = 5 AND b = 5` or `WHERE a = 1 AND b = 10 AND c = 5`). It enables you to specify values in multiple columns by using a list of columns in the `COLUMNS` clause and a set of column values in each `PARTITION ... VALUES IN (value_list)` partition definition clause.

The rules governing regarding data types for the column list used in `LIST COLUMNS(column_list)` and the value list used in `VALUES IN(value_list)` are the same as those for the column list used in `RANGE COLUMNS(column_list)` and the value list used in `VALUES LESS THAN(value_list)`, respectively, except that in the `VALUES IN` clause, `MAXVALUE` is not permitted, and you may use NULL.

There is one important difference between the list of values used for `VALUES IN` with `PARTITION BY LIST COLUMNS` as opposed to when it is used with `PARTITION BY LIST`. When used with `PARTITION BY LIST COLUMNS`, each element in the `VALUES IN` clause must be a set of column values; the number of values in each set must be the same as the number of columns used in the `COLUMNS` clause, and the data types of these values must match those of the columns (and occur in the same order). In the simplest case, the set consists of a single column. The maximum number of columns that can be used in the `column_list` and in the elements making up the `value_list` is 16.

The table defined by the following `CREATE TABLE` statement provides an example of a table using `LIST COLUMNS` partitioning:
```
CREATE TABLE lc (
    a INT NULL,
    b INT NULL
)
PARTITION BY LIST COLUMNS(a,b) (
PARTITION p0 VALUES IN( (0,0), (NULL,NULL) ),
PARTITION p1 VALUES IN( (0,1), (0,2), (0,3), (1,1), (1,2) ),
PARTITION p2 VALUES IN( (1,0), (2,0), (2,1), (3,0), (3,1) ),
PARTITION p3 VALUES IN( (1,3), (2,2), (2,3), (3,2), (3,3) )
);
```

• PARTITIONS num

The number of partitions may optionally be specified with a `PARTITIONS num` clause, where `num` is the number of partitions. If both this clause and any `PARTITION` clauses are used, num must be equal to the total number of any partitions that are declared using `PARTITION` clauses.

    Note
    Whether or not you use a `PARTITIONS` clause in creating a table that is partitioned by `RANGE` or `LIST`, you must still include at least one `PARTITION VALUES` clause in the table definition (see below).

• SUBPARTITION BY

A partition may optionally be divided into a number of subpartitions. This can be indicated by using the optional `SUBPARTITION BY` clause. Subpartitioning may be done by `HASH` or `KEY`. Either of these may be `LINEAR`. These work in the same way as previously described for the equivalent partitioning types. (It is not possible to subpartition by `LIST` or `RANGE`.)

The number of subpartitions can be indicated using the `SUBPARTITIONS` keyword followed by an integer value.

• Rigorous checking of the value used in `PARTITIONS` or `SUBPARTITIONS` clauses is applied and this value must adhere to the following rules:
  • The value must be a positive, nonzero integer.
  • No leading zeros are permitted.
  • The value must be an integer literal, and cannot not be an expression. For example, `PARTITIONS 0.2E+01` is not permitted, even though `0.2E+01` evaluates to 2. (Bug #15890)

• partition_definition

  Each partition may be individually defined using a partition_definition clause. The individual parts making up this clause are as follows:

  * PARTITION partition_name
  Specifies a logical name for the partition.

  * VALUES
  For range partitioning, each partition must include a `VALUES LESS THAN` clause; for list partitioning,
  you must specify a `VALUES IN` clause for each partition. This is used to determine which rows are
  to be stored in this partition. See the discussions of partitioning types in Chapter 22, Partitioning, for
  syntax examples.

  * [STORAGE] ENGINE
  MySQL accepts a `[STORAGE] ENGINE` option for both `PARTITION` and `SUBPARTITION`. Currently,
  the only way in which this option can be used is to set all partitions or all subpartitions to the same
  storage engine, and an attempt to set different storage engines for partitions or subpartitions in the
  same table will give rise to the error `ERROR 1469 (HY000)`: The mix of handlers in the
  partitions is not permitted in this version of MySQL.

  * COMMENT
  An optional `COMMENT` clause may be used to specify a string that describes the partition. Example:
  ```
  COMMENT = 'Data for the years previous to 1999'
  ```
  The maximum length for a partition comment is 1024 characters.

* DATA DIRECTORY and INDEX DIRECTORY

  `DATA DIRECTORY` and `INDEX DIRECTORY` may be used to indicate the directory where, respectively,
  the data and indexes for this partition are to be stored. Both the data_dir and the index_dir must be absolute system path names.
  You must have the `FILE` privilege to use the `DATA DIRECTORY` or `INDEX DIRECTORY` partition option.
  Example:
  ```
  CREATE TABLE th (id INT, name VARCHAR(30), adate DATE)
  PARTITION BY LIST(YEAR(adate))
  (
  PARTITION p1999 VALUES IN (1995, 1999, 2003)
  DATA DIRECTORY = '/var/appdata/95/data'
  INDEX DIRECTORY = '/var/appdata/95/idx',
  PARTITION p2000 VALUES IN (1996, 2000, 2004)
  DATA DIRECTORY = '/var/appdata/96/data'
  INDEX DIRECTORY = '/var/appdata/96/idx',
  PARTITION p2001 VALUES IN (1997, 2001, 2005)
  DATA DIRECTORY = '/var/appdata/97/data'
  INDEX DIRECTORY = '/var/appdata/97/idx',
  PARTITION p2002 VALUES IN (1998, 2002, 2006)
  DATA DIRECTORY = '/var/appdata/98/data'
  INDEX DIRECTORY = '/var/appdata/98/idx'
  );
  ```
  `DATA DIRECTORY` and `INDEX DIRECTORY` behave in the same way as in the `CREATE TABLE` statement's `table_option` clause as used for MyISAM tables.

  One data directory and one index directory may be specified per partition. If left unspecified, the data and indexes are stored by default in the table's database directory.

  The `DATA DIRECTORY` and `INDEX DIRECTORY` options are ignored for creating partitioned tables if `NO_DIR_IN_CREATE` is in effect.

• MAX_ROWS and MIN_ROWS

May be used to specify, respectively, the maximum and minimum number of rows to be stored in the partition. The values for `max_number_of_rows` and `min_number_of_rows` must be positive integers. As with the table-level options with the same names, these act only as “suggestions” to the server and are not hard limits.

• TABLESPACE

May be used to designate an InnoDB file-per-table tablespace for the partition. All partitions must belong to the same storage engine.

Placing InnoDB table partitions in shared InnoDB tablespaces is not supported. Shared tablespaces include the InnoDB system tablespace and general tablespaces.

• subpartition_definition

The partition definition may optionally contain one or more `subpartition_definition` clauses.  Each of these consists at a minimum of the `SUBPARTITION` name, where name is an identifier for the subpartition. Except for the replacement of the `PARTITION` keyword with `SUBPARTITION`, the syntax for a subpartition definition is identical to that for a partition definition.

Subpartitioning must be done by `HASH` or `KEY`, and can be done only on `RANGE` or `LIST` partitions. See Section 22.2.6, “Subpartitioning”.

#### Partitioning by Generated Columns

Partitioning by generated columns is permitted. For example:
```
CREATE TABLE t1 (
    s1 INT,
    s2 INT AS (EXP(s1)) STORED
)
PARTITION BY LIST (s2) (
PARTITION p1 VALUES IN (1)
);
```
Partitioning sees a generated column as a regular column, which enables workarounds for limitations on functions that are not permitted for partitioning (see Section 22.6.3, “Partitioning Limitations Relating to Functions”). The preceding example demonstrates this technique: `EXP()` cannot be used directly in the `PARTITION BY` clause, but a generated column defined using `EXP()` is permitted.

## 13.1.18.1 CREATE TABLE Statement Retention

The original `CREATE TABLE` statement, including all specifications and table options are stored by MySQL when the table is created. The information is retained so that if you change storage engines, collations or other settings using an `ALTER TABLE` statement, the original table options specified are retained. This enables you to change between InnoDB and MyISAM table types even though the row formats supported by the two engines are different.

Because the text of the original statement is retained, but due to the way that certain values and options may be silently reconfigured, the active table definition (accessible through `DESCRIBE` or with `SHOW TABLE STATUS`) and the table creation string (accessible through `SHOW CREATE TABLE`) may report different values.

For InnoDB tables, `SHOW CREATE TABLE` and the `Create_options` column reported by `SHOW TABLE STATUS` show the actual `ROW_FORMAT` and `KEY_BLOCK_SIZE` attributes used by the table. In previous MySQL releases, the originally specified values for these attributes were reported.

## 13.1.18.2 Files Created by CREATE TABLE

For an InnoDB table created in a file-per-table tablespace or general tablespace, table data and associated indexes are stored in an ibd file in the database directory. When an InnoDB table is created in the system tablespace, table data and indexes are stored in the `ibdata*` files that represent the system tablespace. The `innodb_file_per_table` option controls whether tables are created in file-per-table tablespaces or the system tablespace, by default. The `TABLESPACE` option can be used to place a table in a file-per-table tablespace, general tablespace, or the system tablespace, regardless of the `innodb_file_per_table` setting.

For MyISAM tables, the storage engine creates data and index files. Thus, for each MyISAM table `tbl_name`, there are two disk files.

    File                Purpose
    tbl_name.MYD        Data file
    tbl_name.MYI        Index file

Chapter 16, Alternative Storage Engines, describes what files each storage engine creates to represent tables. If a table name contains special characters, the names for the table files contain encoded versions of those characters as described in Section 9.2.3, “Mapping of Identifiers to File Names”.

## 13.1.18.3 CREATE TEMPORARY TABLE Syntax



## 13.1.18.4 CREATE TABLE ... LIKE Syntax



## 13.1.18.5 CREATE TABLE ... SELECT Syntax



## 13.1.18.6 Using FOREIGN KEY Constraints



## 13.1.18.7 Silent Column Specification Changes

## 13.1.18.8 CREATE TABLE and Generated Columns

## 13.1.18.9 Secondary Indexes and Generated Columns

InnoDB supports secondary indexes on virtual generated columns. Other index types are not supported. A secondary index defined on a virtual column is sometimes referred to as a “virtual index”.

A secondary index may be created on one or more virtual columns or on a combination of virtual columns and regular columns or stored generated columns. Secondary indexes that include virtual columns may be defined as `UNIQUE`.

When a secondary index is created on a virtual generated column, generated column values are materialized in the records of the index. If the index is a covering index (one that includes all the columns retrieved by a query), generated column values are retrieved from materialized values in the index structure instead of computed “on the fly”.

There are additional write costs to consider when using a secondary index on a virtual column due to computation performed when materializing virtual column values in secondary index records during `INSERT` and `UPDATE` operations. Even with additional write costs, secondary indexes on virtual columns may be preferable to generated stored columns, which are materialized in the clustered index, resulting in larger tables that require more disk space and memory. If a secondary index is not defined on a virtual column, there are additional costs for reads, as virtual column values must be computed each time the column's row is examined.

Values of an indexed virtual column are MVCC-logged to avoid unnecessary recomputation of generated column values during rollback or during a purge operation. The data length of logged values is limited by the index key limit of 767 bytes for `COMPACT` and `REDUNDANT` row formats, and 3072 bytes for `DYNAMIC` and `COMPRESSED` row formats.
索引虚拟列的值是mvcc日志记录的，以避免在回滚或清除操作期间对生成的列值进行不必要的重新计算。日志值的数据长度受索引键限制，`COMPACT` 和 `REDUNDANT` 行格式限制为767字节，`DYNAMIC` 和 `COMPRESSED` 行格式限制为3072字节。

Adding or dropping a secondary index on a virtual column is an in-place operation.

#### Indexing a Generated Column to Provide a JSON Column Index

As noted elsewhere, JSON columns cannot be indexed directly. To create an index that references such a column indirectly, you can define a generated column that extracts the information that should be indexed, then create an index on the generated column, as shown in this example:
```
mysql> CREATE TABLE jemp (
-> c JSON,
-> g INT GENERATED ALWAYS AS (c->"$.id")),
-> INDEX i (g)
-> );
Query OK, 0 rows affected (0.28 sec)

mysql> INSERT INTO jemp (c) VALUES
> ('{"id": "1", "name": "Fred"}'), ('{"id": "2", "name": "Wilma"}'),
> ('{"id": "3", "name": "Barney"}'), ('{"id": "4", "name": "Betty"}');
Query OK, 4 rows affected (0.04 sec)
Records: 4 Duplicates: 0 Warnings: 0

mysql> SELECT c->>"$.name" AS name
> FROM jemp WHERE g > 2;
+--------+
| name |
+--------+
| Barney |
| Betty |
+--------+
2 rows in set (0.00 sec)

mysql> EXPLAIN SELECT c->>"$.name" AS name
> FROM jemp WHERE g > 2\G
*************************** 1. row ***************************
id: 1
select_type: SIMPLE
table: jemp
partitions: NULL
type: range
possible_keys: i
key: i
key_len: 5
ref: NULL
rows: 2
filtered: 100.00
Extra: Using where
1 row in set, 1 warning (0.00 sec)

mysql> SHOW WARNINGS\G
*************************** 1. row ***************************
Level: Note
Code: 1003
Message: /* select#1 */ select json_unquote(json_extract(`test`.`jemp`.`c`,'$.name'))
AS `name` from `test`.`jemp` where (`test`.`jemp`.`g` > 2)
1 row in set (0.00 sec)
```
(We have wrapped the output from the last statement in this example to fit the viewing area.)

When you use `EXPLAIN` on a `SELECT` or other SQL statement containing one or more expressions that use the `->` or `->>` operator, these expressions are translated into their equivalents using `JSON_EXTRACT()` and (if needed) `JSON_UNQUOTE()` instead, as shown here in the output from `SHOW WARNINGS` immediately following this `EXPLAIN` statement:
```
mysql> EXPLAIN SELECT c->>"$.name"
> FROM jemp WHERE g > 2 ORDER BY c->"$.name"\G
*************************** 1. row ***************************
id: 1
select_type: SIMPLE
table: jemp
partitions: NULL
type: range
possible_keys: i
key: i
key_len: 5
ref: NULL
rows: 2
filtered: 100.00
Extra: Using where; Using filesort
1 row in set, 1 warning (0.00 sec)

mysql> SHOW WARNINGS\G
*************************** 1. row ***************************
Level: Note
Code: 1003
Message: /* select#1 */ select json_unquote(json_extract(`test`.`jemp`.`c`,'$.name')) AS
`c->>"$.name"` from `test`.`jemp` where (`test`.`jemp`.`g` > 2) order by
json_extract(`test`.`jemp`.`c`,'$.name')
1 row in set (0.00 sec)
```

See the descriptions of the -> and ->> operators, as well as those of the JSON_EXTRACT() and JSON_UNQUOTE() functions, for additional information and examples.

This technique also can be used to provide indexes that indirectly reference columns of other types that cannot be indexed directly, such as GEOMETRY columns.

https://v.qq.com/x/cover/23fo5w35atkawom.html
https://www.yangguiweihuo.com/12/12151/20057682.html

http://www.5ifxw.com/vip/
https://www.iqiyi.com/v_19rqrh8vhw.html

https://avgle.com/search/videos?search_query=hqis-&search_type=videos&page=2
https://avgle.com/search/videos/%E5%B0%8F%E8%A5%BF%E3%81%BE%E3%82%8A%E3%81%88
