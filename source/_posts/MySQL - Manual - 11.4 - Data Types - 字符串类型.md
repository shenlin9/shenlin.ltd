---
title: MySQL - Manual - 11.4 - Data Types - 字符串类型
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 字符串类型

<!--more-->

字符串类型有 `CHAR`, `VARCHAR`, `BINARY`, `VARBINARY`, `BLOB`, `TEXT`, `ENUM` 和
`SET`。本节描述这些类型如何工作，以及如何在查询中使用它们。对于字符串类型的存储
需求，参考 Section 11.8, “Data Type Storage Requirements”.

## 11.4.1 The CHAR and VARCHAR Types

`CHAR` 和 `VARCHAR` 类型是相似的，但是
* 它们存储和检索的方式不同。
* 它们的最大长度也不同
* 是否保留尾部的空格也不同。

`CHAR` 和 `VARCHAR` 类型声明时使用一个长度来表示您想要存储的字符的最大数量。例如
，`CHAR(30)` 可以容纳 30 个字符。

`CHAR` 列的长度固定为您创建表时声明的长度。长度可以是从 0 到 255 的任何值。当
`CHAR` 值被存储时，它们会被使用空格向右填充到指定的长度。当检索 `CHAR` 值时，除
非启用 `PAD_CHAR_TO_FULL_LENGTH` SQL 模式，否则将删除尾随空格。

`VARCHAR` 列中的值是长度可变的字符串。长度可以指定为从 0 到 65,535 的值。
`VARCHAR` 的有效最大长度取决于最大行大小（65,535 字节，在所有列中共享）和所使用
的字符集。请参阅 Section C.10.4, “Limits on Table Column Count and Row Size”。

与 `CHAR` 相比，`VARCHAR` 值被存储为 1 字节或 2 字节长度前缀加数据。长度前缀表示
值中的字节数。如果值不超过 255 个字节，则列使用一个长度字节，如果值需要超过 255
字节，则需要两个长度字节。

如果没有启用严格的 SQL 模式，并且您将一个值赋给 `CHAR` 或 `VARCHAR` 列，该值超出
了列的最大长度，那么该值就会被截断以适应列长度，并产生一个警告。对于截断非空格字
符，您可以触发错误（而不是警告），并使用严格的 SQL 模式抑制插入值。参见 Section
5.1.10, “Server SQL Modes”。

对于 `VARCHAR` 列，不管使用的是哪种 SQL 模式，在插入之前，超过列长度的尾部空格会
被截断，并且产生一个警告。

对于 `CHAR` 列，不管使用的是哪种 SQL 模式，插入值中的多余尾部空格会被静默的截断。

`VARCHAR` 值在存储时不会被填充。与标准 SQL 一致，存储和检索值时保留尾随空格。

下表展示了 `CHAR` 和 `VARCHAR` 之间的区别，它展示了将各种字符串值存储到
`CHAR(4)` 和 `VARCHAR(4)` 列的结果（假设此列使用了一个单字节字符集，如 `latin1`
）。

    Value      CHAR(4)     Storage Required    VARCHAR(4)       Storage Required
    ''          ' '             4 bytes             ''              1 byte
    'ab'        'ab '           4 bytes             'ab'            3 bytes
    'abcd'      'abcd'          4 bytes             'abcd'          5 bytes
    'abcdefgh'  'abcd'          4 bytes             'abcd'          5 bytes

上表中最后一行所示的值仅在不使用严格模式时才适用；如果 MySQL 以严格的模式运行，
那么超过列长度的值就不会被存储，只会产生错误结果。

InnoDB 将大于或等于 768 字节的固定长度的字段编码为可变长度的字段，这些字段可以存
储在页面之外。例如，如果字符集的最大字节长度大于 3，那么 `CHAR(255)` 的列可以超
过 768 字节，就像 `utf8mb4` 一样。

如果一个给定的值被存储到 `CHAR(4)` 和 `VARCHAR(4)` 的列中，那么从列中检索的值并
不总是相同的，因为在检索时尾部的空格会从 `CHAR` 列中删除。

下面的例子说明了这种差异：
```
mysql> CREATE TABLE vc (v VARCHAR(4), c CHAR(4));
Query OK, 0 rows affected (0.01 sec)

mysql> INSERT INTO vc VALUES ('ab ', 'ab ');
Query OK, 1 row affected (0.00 sec)

mysql> SELECT CONCAT('(', v, ')'), CONCAT('(', c, ')') FROM vc;
+---------------------+---------------------+
| CONCAT('(', v, ')') | CONCAT('(', c, ')') |
+---------------------+---------------------+
| (ab )               | (ab)                |
+---------------------+---------------------+
1 row in set (0.06 sec)
```

`CHAR` 和 `VARCHAR` 列中的值按照分配给列的字符集校对规则进行排序和比较。

大部分 MySQL 校对规则都有一个称为 `PAD SPACE` 的填充属性，例外是基于 `UCA 9.0.0`
和更高版本的 Unicode 校对规则，它们有一个称为 `NO PAD` 的填充属性。(see Section
10.10.1, “Unicode Character Sets”).

要确定一个校对规则的填充属性，使用 `INFORMATION_SCHEMA COLLATIONS` 表里的
`PAD_ATTRIBUTE` 列。

填充属性确定了非二进制字符串(`CHAR`, `VARCHAR`, `TEXT`)在进行比较时如何对待它尾
部的空格，具有 `NO PAD` 填充属性的校对规则对待字符串末尾的空格像其他任何字符一样
，具有 `PAD SPACE` 填充属性的校对规则，比较时尾部的空格是无关紧要的，字符串比较
时不考虑任何的尾部空格，这里上下文的“比较”不包含使用 `LIKE` 操作符的模式匹配，
模式匹配时尾部空格是很重要的。例如：
```
mysql> CREATE TABLE names (myname CHAR(10));
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO names VALUES ('Monty');
Query OK, 1 row affected (0.00 sec)

mysql> SELECT myname = 'Monty', myname = 'Monty ' FROM names;
+------------------+--------------------+
| myname = 'Monty' | myname = 'Monty ' |
+------------------+--------------------+
| 1                | 1                  |
+------------------+--------------------+
1 row in set (0.00 sec)

mysql> SELECT myname LIKE 'Monty', myname LIKE 'Monty ' FROM names;
+---------------------+-----------------------+
| myname LIKE 'Monty' | myname LIKE 'Monty ' |
+---------------------+-----------------------+
| 1                   | 0                    |
+---------------------+-----------------------+
1 row in set (0.00 sec)
```
这适用于所有 MySQL 版本，且不受服务器 SQL 模式影响。

    注意：
    关于 MySQL 字符集和校对规则的更多信息，参考 Chapter 10, Character Sets,
    Collations, Unicode.
    关于存储需求的更多信息，参考 Section 11.8, “Data Type Storage Requirements”

对于那些尾部填充字符被剥离（stripped）或比较时忽略填充字符的情况，
如果一个列有一个要求唯一值的索引，当插入的多个列值只是尾部的填充字符数量有所不同
时，将会导致一个重复键错误。例如，如果一张表包含 'a'，那么再尝试存储 'a ' 就会导
致一个重复键错误（duplicate-key error）。

## 11.4.2 The BINARY and VARBINARY Types

`BINARY` 和 `VARBINARY` 类型类似于 `CHAR` 和 `VARCHAR`，除了它们包含的是二进制字
符串，而不是非二进制字符串。也就是说，它们包含的是字节字符串而不是字符字符串，这
意味着它们使用二进制字符集和校对规则，且比较和排序是基于字节的数字值。

`BINARY` 和 `VARBINARY` 允许的最大长度和 `CHAR` 和 `VARCHAR` 一样，除了 `BINARY`
和 `VARBINARY` 的长度是字节长度而不是字符长度。

`BINARY` 和 `VARBINARY` 数据类型不同于 `CHAR BINARY` 和 `VARCHAR BINARY` 数据类
型，后两种类型，`BINARY` 属性不会使得列被作为二进制字符串列对待，而是使得列的字
符集使用二进制校对规则 `_bin`，而列本身包含的是非二进制的字符字符串，而不是二进
制的字节字符串。例如：`CHAR(5) BINARY` 被当作 `CHAR(5) CHARACTER SET utf8mb4
COLLATE utf8mb4_bin` 对待（假设默认的字符集是 `utf8mb4` 的话）。这是不同于
`BINARY(5)` 的，后者存储的是 5 字节的二进制字符串，使用的是二进制字符集和校对规
则。For information about differences between binary strings and binary
collations for nonbinary strings，see Section 10.8.5, “The binary Collation
Compared to `_bin` Collations”.

如果没有启用严格的 SQL 模式，并且您将一个值赋给 `BINARY` 或 `VARBINARY` 列，该值
超出了列的最大长度，那么该值就会被截断以适应列长度，并产生一个警告。对于截断您可
以触发错误（而不是警告），并使用严格的 SQL 模式抑制插入值。参见 Section 5.1.10,
“Server SQL Modes”。

当 `BINARY` 值被存储时，被使用填充值向右填充到指定的长度，填充值是 `0x00` (0 字
节)。在插入时值被使用 `0x00` 向右填充，且查询时不会移除尾部的字节。在比较时所有
字节都很重要，包括执行 `ORDER BY` 和 `DISTINCT` 操作。在比较时 `0x00` 字节和空格
不同：`0x00 < space`.

例如：对于一个 `BINARY(3)` 列，'a ' 插入后变为 'a \0'，'a\0' 插入后变为 'a\0\0'
。执行搜索时两个插入的值都保持不变。

对于 `VARBINARY`，插入时没有填充，搜索时也没有字节被剥离。比较时所有字节都很重要
，包括 `ORDER BY` 和 `DISTINCT` 操作时。`0x00` 字节和空格在比较时不一样，`0x00 <
space`.

对于那些尾部填充字节被剥离（stripped）或比较时忽略填充字节的情况，如果一个列有一
个要求唯一值的索引，当插入的多个列值只是尾部的填充字节数量有所不同时，将会导致一
个重复键错误。例如，如果一张表包含 'a'，那么再尝试存储 'a\0' 就会导致一个重复键
错误（duplicate-key error）。

如果您打算使用 `binary` 数据类型来存储二进制数据，并且您要求检索的值与存储的值完
全相同，那么您应该仔细考虑前面的填充和剥离特性。下面的例子说明了 `binary` 值的
`0x00` 填充如何影响列值比较：
```
mysql> CREATE TABLE t (c BINARY(3));
Query OK, 0 rows affected (0.01 sec)

mysql> INSERT INTO t SET c = 'a';
Query OK, 1 row affected (0.01 sec)

mysql> SELECT HEX(c), c = 'a', c = 'a\0\0' from t;
+--------+---------+-------------+
| HEX(c) | c = 'a' | c = 'a\0\0' |
+--------+---------+-------------+
| 610000 | 0       | 1           |
+--------+---------+-------------+
1 row in set (0.09 sec)
```

如果需要检索到的值必须与没有填充的存储值相同，那么最好使用 `VARBINARY` 或 `BLOB`
数据类型。

## 11.4.3 The BLOB and TEXT Types

`BLOB` 是一个二进制大对象，它可以容纳数量可变的数据。四个 `BLOB` 类型是
`TINYBLOB`，`BLOB`，`MEDIUMBLOB` 和 `LONGBLOB` ，区别在于值的最大长度不同。四个
`TEXT` 类型是 `TINYTEXT` 的 `TEXT`  `MEDIUMTEXT` 和 `LONGTEXT` ，它们对应于四个
`BLOB` 类型，并且具有相同的最大长度和存储需求。参见 Section 11.8, “Data Type
Storage Requirements”。

`BLOB` 值被作为二进制字符串处理（字节字符串），它们使用 `binary` 字符集和校对规
则，比较和排序是基于列值的字节数字值。

`TEXT` 值被作为非二进制字符串处理（字符字符串），它们使用不同于 `binary` 的字符
集，比较和排序是基于字符集的校对规则。

如果禁用了严格的 `SQL` 模式，且为 `BLOB` 或 `TEXT` 列赋值超过了列的最大长度，则
值会被截断来适应列长，并产生警告。若截断的是非空格字符，你可以触发一个错误而不是
警告，并使用严格的 SQL 模式抑制插入值。参考 Section 5.1.10, “Server SQL Modes”

无论是什么 SQL 模式，插入到 `TEXT` 列的值多余的尾部空格被截断后都有产生警告。

对于 `TEXT` 和 `BLOB` 列，插入时没有填充，检索选择时没有删除字节。但如果一个
`TEXT` 列被编入了索引，那么比较索引条目最终还是会填充空格（index entry
comparisons are space-padded at the end）。这意味着，如果索引需要唯一的值，那么
对于只是尾部空格数量不同的值，就会出现重复键错误。例如，如果一张表包含 'a' ，再
尝试存储 'a ' 就会导致一个重复键错误。这对于 `BLOB` 列来说是不正确的。

在大多数方面，可以把 `BLOB` 列看作是一个任意大的 `VARBINARY` 列。类似地，您可以
将 `TEXT` 列看作是 `VARCHAR` 列。 `BLOB` 和 `TEXT` 在以下几个方面不同于
`VARBINARY` 和 `VARCHAR`：

* 对于 `BLOB` 和 `TEXT` 列的索引，必须指定一个索引前缀长度。对于 `CHAR` 和
  `VARCHAR`，前缀长度是可选的。参考 Section 8.3.5, “Column Indexes”

* `BLOB` 和 `TEXT` 列不能有 `DEFAULT` 默认值

如果对 `TEXT` 数据类型使用 `BINARY` 属性，则列就被分配列字符集所对应的二进制校对
规则（`_bin`）

`LONG` 和 `LONG VARCHAR` 映射到 `MEDIUMTEXT` 数据类型，这是一个兼容性特性。MySQL
Connector/ODBC 将 `BLOB` 值解释为 `LONGVARBINARY`，`TEXT` 值解释为 `LONGVARCHAR`

因为 `BLOB` 和 `TEXT` 的值可能非常长，所以使用它们时可能会遇到的一些限制：

* 在排序时，只使用列的第一个 `max_sort_length` 字节。`max_sort_length` 的缺省值
  是 1024。通过在服务器启动或运行时增加 `max_sort_length` 的值，您可以使更多的字
  节在排序或分组时变得更重要。任何客户端都可以更改其会话变量 `max_sort_length`
  的值：

    ```
    mysql> SET max_sort_length = 2000;
    mysql> SELECT id, comment FROM t ORDER BY comment;
    ```

* 一个正处理的查询结果中的 `BLOB` 或 `TEXT` 列的实例使用临时表会导致服务器使用磁
  盘上的表，而不是内存中的表，因为存储引擎 `MEMORY` 不支持这些数据类型(参见
  Section 8.4.4, “Internal Temporary Table Use in MySQL”)。磁盘的使用会导致性
  能损失，所以应该只有在真正需要的时候才在查询结果中包含 `BLOB` 或 `TEXT` 列。例
  如，避免使用选择所有列的 `SELECT *`。

* `BLOB` 或 `TEXT` 对象的最大大小取决于它的类型，但是您实际上可以在客户端和服务
  器之间传输的最大值是由可用内存的数量和通信缓冲区的大小决定的。您可以通过改变
  `max_allowed_packet` 变量的值来更改电文缓冲区的大小，但是您必须同时在服务器和
  客户端程序这样做。例如， `mysql` 和 `mysqldump` 都可以支持你改变客户端
  `max_allowed_packet` 的值。
  参考：
  Section 5.1.1, “Configuring the Server”,
  Section 4.5.1, “mysql — The MySQL Command- Line Tool”, 
  Section 4.5.4, “mysqldump — `A` Database Backup Program”. 
  您还可能想要比较数据包的大小和按照存储需求存储的数据对象的大小，参考：Section
  11.8, “Data Type Storage Requirements”

每一个 `BLOB` 或 `TEXT` 值都在内部由一个单独分配的对象表示。这与所有其他数据类型
形成了对比，它们在打开表时，每一列整体分配一次存储。

在某些情况下，将二进制数据例如媒体文件存储在 `BLOB` 或 `TEXT` 列中可能是合适的。
你可能会发现 MySQL 的字符串处理函数对于处理这些数据很有用。参见 Section 12.5, “
String Functions”。出于安全性和其他原因，通常使用应用程序代码而不是向应用程序用
户提供 `FILE` 特权是更好的。您可以在 MySQL 论坛 (http://forums.mysql.com/) 中讨
论各种语言和平台的细节。

## 11.4.4 The ENUM Type

`ENUM` 是一个字符串对象，它的值是从一个允许值列表中挑选出来的，这些值是创建表时
在列规范中显式列出的。

它有这些优点：
* 在列值为一组有限的可能值的情况下压缩数据存储。您指定为输入值的字符串被自动编码
  为数字。 参考 Section 11.8, “Data Type Storage Requirements” 查看 `ENUM` 类
  型的存储需求。
* 可读的查询和输出。在查询结果中，这些数字被转换回相应的字符串。

这些潜在的问题需要考虑：
* 如果您让枚举值看起来像数字，那么很容易将文字值与它们的内部索引号混淆，如枚举限
  制（Enumeration Limitations，本文下面章节）中所解释的那样。
* 在 `ORDER BY` 子句中使用 `ENUM` 列需要特别的注意，就像枚举排序（Enumeration
  Sorting，本文下面章节）中解释的一样。
* 创建和使用枚举列 Creating and Using `ENUM` Columns
* 枚举文字的索引值 Index Values for Enumeration Literals
* 对枚举文字的处理 Handling of Enumeration Literals
* 空或 NULL 枚举值 Empty or NULL Enumeration Values
* 枚举排序         Enumeration Sorting
* 枚举限制         Enumeration Limitations

### Creating and Using ENUM Columns

枚举值必须是引起来的字符串字面量。例如，您可以创建一个包含 `ENUM` 列的表：
```
CREATE TABLE shirts (
    name VARCHAR(40),
    size ENUM('x-small', 'small', 'medium', 'large', 'x-large')
);

INSERT INTO shirts (name, size) VALUES ('dress shirt','large'), ('t-shirt','medium'),
('polo shirt','small');

SELECT name, size FROM shirts WHERE size = 'medium';
+---------+--------+
| name | size |
+---------+--------+
| t-shirt | medium |
+---------+--------+

UPDATE shirts SET size = 'small' WHERE size = 'large';
COMMIT;
```
将 100 万行 'medium' 值插入到该表中需要 100 万字节的存储空间，而如果您将实际的字
符串 'medium' 存储在 `VARCHAR` 列中，则需要 600 万字节。

### Index Values for Enumeration Literals

每个枚举值都有一个索引：
* 列规范中列出的元素都被分配了索引号，从1开始。
* 空字符串错误值的索引为 0。这意味着您可以使用下列 `SELECT` 语句来查找分配了无效
  的 `ENUM` 值的行：
    ```
    mysql> SELECT * FROM tbl_name WHERE enum_col=0;
    ```
* `NULL` 值的索引是 `NULL` 
* 这里的“索引”一词指的是枚举值列表中的一个位置。它与表索引无关。

例如，一个指定为 `ENUM('Mercury', 'Venus', 'Earth')` 的列可以包含如下显示的任何
值。每个值的索引如下所示：

    Value       Index
    -----------------
    NULL        NULL
    ''          0
    'Mercury'   1
    'Venus'     2
    'Earth'     3

一个 `ENUM` 列最多可以有 65,535 个不同的元素。

如果您在一个数值上下文中检索 `ENUM` 值，那么就会返回此列值的索引。例如，您可以像
下面这样从 `ENUM` 列中检索数字值：
```
mysql> SELECT enum_col+0 FROM tbl_name;
```

诸如 `SUM()` 或 `AVG()` 之类需要数值参数的函数，在必要时会将参数转换为数字。对于
`ENUM` 的值，会在计算中使用值的索引号。

### Handling of Enumeration Literals

当一个表被创建时，表定义中 `ENUM` 成员值后面的空格会被自动删除。

当检索时，储存在 `ENUM` 列中的值将使用列定义时使用的字母大小写来显示。注意，
`ENUM` 列可以被分配字符集和校对规则。对于二进制或区分大小写的校对规则，在给列赋
值时，将把字母大小写考虑进去。

如果你把一个数字存储到一个 `ENUM` 列中，那么这个数字就会被当作可能值的索引，而存
储的值就是使用该索引的枚举成员。(然而，这与 `LOAD DATA` 不兼容， `LOAD DATA` 将
所有输入都视为字符串)。如果数字值被引号引用，但枚举值列表中没有匹配的字符串时，
那么它仍然被解释为一个索引。由于这些原因，不建议用看起来像数字的枚举值来定义
`ENUM` 列，因为这很容易让人感到困惑。例如，下面的列有字符串值枚举成员 `0`、`1`
和 `2` ，但是数字索引值为 1、2 和 3：
```
numbers ENUM('0','1','2')
```

如果你存储 2，它被解释为一个索引值，然后变成存储 '1'（索引 2 的值）。如果你存储
'2'，它匹配一个枚举值，所以它被存储为 '2'。如果您存储 '3'，它不匹配任何枚举值，
因此它被当作一个索引来对待，并变成 '2'（索引 3 的值）。
```
mysql> INSERT INTO t (numbers) VALUES(2),('2'),('3');

mysql> SELECT * FROM t;
+---------+
| numbers |
+---------+
| 1 |
| 2 |
| 2 |
+---------+
```

要确定一个 `ENUM` 列的所有可能值，请使用 `SHOW COLUMNS FROM tbl_name LIKE
'enum_col'`，并在输出的 `Type` 列中解析 `ENUM` 定义。

在 C API 中，`ENUM` 值作为字符串返回。有关使用结果集元数据来将它们与其他字符串区
分开来的信息，请参阅 Section 27.7.5, “C API Data Structures”。

### Empty or NULL Enumeration Values

在某些情况下枚举值也可以是的空字符串（''）或 NULL：

* 如果您将一个无效的值插入到 `ENUM` 中（也就是说，没有出现在允许值列表中的字符串
  ），则结果是插入了一个空字符串作为特殊的错误值。这个字符串可以与一个“正常”的
  空字符串区分开来，因为这个字符串的数值为 0。有关枚举值的数值索引的详细信息，请
  参阅枚举字面量的索引值。

  如果启用了严格的 SQL 模式，尝试插入无效的 ENUM 值会导致错误。

* 如果一个 `ENUM` 列被声明为允许 `NULL` ，那么 `NULL` 值是此列的有效值，它的默认
  值也是 `NULL` 。如果 `ENUM` 列被声明为 `NOT NULL` ，它的默认值是允许值列表的第
  一个元素。

### Enumeration Sorting

 `ENUM` 的值是根据它们的索引编号排序的，索引编号由枚举成员在列规范中列出的顺序决
 定。例如，对于 `ENUM`(`b`, `a` ) 排序时 `b` 在 `a` 之前。空字符串排在非空字符串
 之前，NULL 值排在所有其他枚举值之前。

在对 `ENUM` 列使用 `ORDER BY` 子句时，请使用以下任意一项技巧来防止意外结果：
* 按字母顺序指定“ENUM”列表。
* 确保列是通过编码 `ORDER BY CAST(col AS CHAR)` 或 `ORDER BY CONCAT(col)` 按词法
  排序的，而不是由索引序号排序的。

### Enumeration Limitations

枚举值不能是表达式，即使是计算为字符串值的表达式。

例如，这个 `CREATE TABLE` 语句不起作用，因为 `CONCAT` 函数不能用来构造枚举值：
```
CREATE TABLE sizes (
    size ENUM('small', CONCAT('med','ium'), 'large')
);
```

您也不能使用用户变量作为枚举值。下面语句不起作用：
```
SET @mysize = 'medium';

CREATE TABLE sizes (
    size ENUM('small', @mysize, 'large')
);
```

我们强烈建议您不要使用数字作为枚举值，因为通过适当的 `TINYINT` 或`SMALLINT` 类型
它没有节省存储空间，并且如果你引用 `ENUM` 值时不正确，很容易混淆字符串和底层数字
值。如果您确实使用数字作为枚举值，请始终用引号括起来。如果省略了引号，则该数字被
视为一个索引。查看上面章节的 Handling of Enumeration Literals 枚举文字的处理，看
看即使一个引用的数字也是如何错误地被用作数字索引值。

定义中的重复值会导致警告，或者如果启用了严格的 SQL 模式，则会出现错误。

## 11.4.5 The SET Type

`SET` 是一个字符串对象，它可以有零个或多个值，每个值都必须从表创建时指定的允许值
列表中选择。由多个集合成员组成的 `SET` 列值被指定为用逗号分隔的成员。这样做的结
果是：`SET` 成员值本身不应该包含逗号。

例如，一个被指定为 `SET('one', 'two') NOT NULL` 的列可以使用任意下列值：
```
''
'one'
'two'
'one,two'
```

`SET` 列最多可以有 64 个不同的成员。

定义中的重复值会导致警告，或者如果启用了严格的 SQL 模式，则会出现错误。

当一个表被创建时，表定义中的 `SET` 成员值中尾部的空格会被自动删除。

当检索时，储存在 `SET` 列中的值将使用列定义时使用的字母大小写来显示。注意，
`SET` 列可以被分配字符集和校对规则。对于二进制或区分大小写的校对规则，在给列赋
值时，将把字母大小写考虑进去。

MySQL 用数值存储 `SET` 值，存储值的低阶位对应于第一个集合成员。如果您在一个数字
上下文中检索 `SET` 值，那么检索到的值的位集合（bits set）将与组成列值的集合成员
相对应。例如，您可以像这样从 `SET` 列中检索数值：
```
mysql> SELECT set_col+0 FROM tbl_name;
```

如果一个数字被存储到一个 `SET` 列中，那么在数字的二进制表示中设置的位元决定了列
值中的集合成员。对于指定为 `SET ('a','b','c','d')` 的列，成员十进制值和二进制值
表示如下：

    SET Member  Decimal Value   Binary Value
    -----------------------------------------
    'a'             1               0001
    'b'             2               0010
    'c'             4               0100
    'd'             8               1000

如果你给这一列赋值 9，那就是二进制数 `1001`，所以第一个和第四个 `SET` 值成员 'a'
和 'd' 被选中，结果值是 'a,d'。

对于包含不止一个 `SET` 元素的值，当插入值时，元素列出的顺序无关紧要。在值中列出
的给定元素的次数也不重要。当稍后检索值时，值中的每一个元素都会出现一次，其中的元
素按照表创建时指定的顺序列出。例如，假设一个列被指定为 `SET('a','b','c','d')`：
```
mysql> CREATE TABLE myset (col SET('a', 'b', 'c', 'd'));
```

如果你插入值 'a,d', 'd,a', 'a,d,d', 'a,d,a', and 'd,a,d':
```
mysql> INSERT INTO myset (col) VALUES
-> ('a,d'), ('d,a'), ('a,d,a'), ('a,d,d'), ('d,a,d');
Query OK, 5 rows affected (0.01 sec)
Records: 5 Duplicates: 0 Warnings: 0
```

当检索的时候，所有这些值都显示为'a,d'：
```
mysql> SELECT col FROM myset;
+------+
| col |
+------+
| a,d |
| a,d |
| a,d |
| a,d |
| a,d |
+------+
5 rows in set (0.04 sec)
```

如果你将一个 `SET` 列设置为一个不受支持的值，那么该值将被忽略，并发出警告：
```
mysql> INSERT INTO myset (col) VALUES ('a,d,d,s');
Query OK, 1 row affected, 1 warning (0.03 sec)

mysql> SHOW WARNINGS;
+---------+------+------------------------------------------+
| Level   | Code | Message |
+---------+------+------------------------------------------+
| Warning | 1265 | Data truncated for column 'col' at row 1 |
+---------+------+------------------------------------------+
1 row in set (0.04 sec)

mysql> SELECT col FROM myset;
+------+
| col |
+------+
| a,d |
| a,d |
| a,d |
| a,d |
| a,d |
| a,d |
+------+
6 rows in set (0.01 sec)
```
可见虽然值 s 被忽略，但 a 和 d 还是插入成功了

但如果启用了严格的 SQL 模式，尝试插入无效的 SET 值会导致错误。

`SET` 值按数字排序。 `NULL` 值排序在非 NULL 值之前。

诸如 `SUM()` 或 `AVG()` 之类需要数值参数的函数，必要时可以将参数转换为数字。对于
`SET` 值，转换操作会导致数值的使用。

通常使用 `FIND_IN_SET()` 函数和 `LIKE` 操作符搜索 `SET` 值：
```
mysql> SELECT * FROM tbl_name WHERE FIND_IN_SET('value',set_col)>0;
mysql> SELECT * FROM tbl_name WHERE set_col LIKE '%value%';
```
第一条语句搜索 `setcol` 列中包含集合成员 `value` 的行。第二个相似，但不一样：它
搜索列 `setcol` 任何位值包含 `value` 的行，即使是另一个集合成员的子串。

也允许使用下列语句：
```
mysql> SELECT * FROM tbl_name WHERE set_col & 1;
mysql> SELECT * FROM tbl_name WHERE set_col = 'val1,val2';
```
第一条语句查找包含第一个集合成员的值。第二条语句查找一个精确匹配。对第二种类型的
比较要小心。将集合值与 'val2,val1' 比较和与 'val1,val2' 比较会返回不同的结果。应
该按照列定义中的顺序来指定这些值。

要确定 `SET` 列的所有可能值，请使用 `SHOW COLUMNS FROM tbl_name LIKE set_col`，
并在输出的 `Type` 列中解析 `SET` 定义。

在 C API 中，`SET` 值作为字符串返回。有关使用结果集元数据来区分它们和其他字符串
的信息，参考 Section 27.7.5, “C API Data Structures”.
