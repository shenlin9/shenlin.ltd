---
title: MySQL - Manual - 11.1 - Data Types - 数据类型概述
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 数据类型概述

<!--more-->

MySQL 支持多种类型的 SQL 数据类型：数值类型、日期和时间类型、字符串（字符和字节
）类型、空间类型和 JSON 数据类型。

本章提供了这些数据类型的概述，对应的每章对每个类别中类型的属性进行了更详细的描述
，并对数据类型存储需求进行了总结。最初的概述是有意简短的。应该查询本章后面的更详
细的描述，以获得关于特定数据类型的附加信息，比如您可以指定值的允许格式。

数据类型描述使用这些约定：

* `M` ：
    * 表示整数类型的最大显示宽度。
    * 对于浮点数和定点类型，M 是可以存储的数字的总数（精度）。
    * 对于字符串类型，M 是最大长度。
    * M 的最大允许值取决于数据类型。

* `D` ：
    * 适用于浮点数和定点类型，并指出小数点后的位数（刻度）。
    * 最大值是30，但不应该大于M-2。

* `fsp` 适用于TIME、DATETIME、和 TIMESTAMP 类型，并表示分数秒精度；也就是，小数
  点后面分秒部分的位数。如果给定 fsp 值则必须在 0 到 6 之间。0 的值表示没有小数
  部分。如果省略，默认的精度是 0。（这与标准的 SQL 默认值 6 不同，是为了与之前的
  MySQL 版本兼容。）

* 方括号(`[` and `]`) 表示类型定义的可选部分。 

## 11.1.1 Numeric Type Overview

下面是对数字数据类型的总结。有关数字类型的属性和存储需求的附加信息，请参见
Section 11.2, “Numeric Types”, Section 11.8, “Data Type Storage Requirements
”

`M` 表示整数类型的最大显示宽度。最大显示宽度是 255。显示宽度与类型所能包含值的范
围无关，如 Section 11.2, “Numeric Types”所述。对于浮点数和定点数类型，`M` 是可
以存储的数字的总数。

如果你为一列指定了 `ZEROFILL`，MySQL 自动为此列添加 `UNSIGNED` 属性。

数值数据类型允许 `UNSIGNED` 也允许 `SIGNED`，但是这些数据类型默认是有符号的，所
以 `SIGNED` 属性没有效果。

`SERIAL` 是 `BIGINT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE` 的别名

在整数列的定义中 `SERIAL DEFAULT VALUE` 是 `NOT NULL AUTO_INCREMENT UNIQUE` 的别
名

警告：
当你在两个整数值之间使用减法，且其中一个整数是 `UNSIGNED` 时，则结果是无符号的，
除非启用了 `NO_UNSIGNED_SUBTRACTION` SQL 模式。参考：Section 12.10, “

### BIT[(M)]

一个位值的类型。`M` 表示每个值的位数，从 1 到 64。如果省略了 `M`，默认值是 1。

### BOOL, BOOLEAN

这些类型是 TINYINT(1) 的同义词。0 值为 false，非 0 值为 true：
```
mysql> SELECT IF(0, 'true', 'false');
+------------------------+
| IF(0, 'true', 'false') |
+------------------------+
| false                  |
+------------------------+

mysql> SELECT IF(1, 'true', 'false');
+------------------------+
| IF(1, 'true', 'false') |
+------------------------+
| true                   |
+------------------------+

mysql> SELECT IF(2, 'true', 'false');
+------------------------+
| IF(2, 'true', 'false') |
+------------------------+
| true                   |
+------------------------+
```
其实，TRUE 和 FALSE 都仅仅是 1 和 0 的别名，如下所示：
```
mysql> SELECT IF(0 = FALSE, 'true', 'false');
+--------------------------------+
| IF(0 = FALSE, 'true', 'false') |
+--------------------------------+
| true |
+--------------------------------+

mysql> SELECT IF(1 = TRUE, 'true', 'false');
+-------------------------------+
| IF(1 = TRUE, 'true', 'false') |
+-------------------------------+
| true |
+-------------------------------+

mysql> SELECT IF(2 = TRUE, 'true', 'false');
+-------------------------------+
| IF(2 = TRUE, 'true', 'false') |
+-------------------------------+
| false |
+-------------------------------+

mysql> SELECT IF(2 = FALSE, 'true', 'false');
+--------------------------------+
| IF(2 = FALSE, 'true', 'false') |
+--------------------------------+
| false |
+--------------------------------+
```
最后两个语句显示如此结果，是因为 2 既不等于 1 也不等于 0。

### TINYINT[(M)] [UNSIGNED] [ZEROFILL]

1 字节

一个非常小的整数。有符号的范围是 -128 到 127。无符号范围是 0 到 255。

### SMALLINT[(M)] [UNSIGNED] [ZEROFILL]

2 字节

一个小整数。有符号范围是-32768 到 32767。无符号范围是 0 到 65535。

### MEDIUMINT[(M)] [UNSIGNED] [ZEROFILL]

3 字节

一个中等大小的整数。有符号范围是 -8388608 至 8388607。无符号范围是 0 到 16777215。

### INT[(M)] [UNSIGNED] [ZEROFILL]

4 字节

一个正常大小的整数。有符号范围是 -2147483648 至 2147483647。无符号范围是 0 到
4294967295。

### INTEGER[(M)] [UNSIGNED] [ZEROFILL]

INT 的别名

### BIGINT[(M)] [UNSIGNED] [ZEROFILL]

8 字节

一个大型整数。有符号范围是 -9223372036854775808 至 9223372036854775807。无符号范
围是 0 到 18446744073709551615.

`SERIAL` 是 `BIGINT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE` 的别名

关于 `BIGINT` 列，您应该注意一些事情：

* 所有算法都是使用有符号 `BIGINT` 或 `DOUBLE` 值实现的，所以除非是用于位函数
  ，否则不应该使用大于 `9223372036854775807` (63 bits)的无符号大整数，如果这
  样做了，当把 `BIGINT` 值转换为 `DOUBLE` 时因为舍入误差（rounding errors）
  的关系结果里的最后一些位可能出错。

  MySQL 可以在以下情况下处理 BIGINT：
  * 当使用整数在 BIGINT 列中存储大型无符号值时
  * 在 MIN(col_name) 或 MAX(col_name) 里 col_name 引用的是一个 BIGINT 列
  * 当使用运算符(`+, -, *` 等等)的两个操作数都是整型时

* 您总是可以通过使用字符串从而在 BIGINT 列中存储准确的整数值。在这种情况下，
  MySQL 执行一个字符串到数字转换，不涉及中间的双精度表示。

* 当两个操作数都是整数值时，`-, +, *` 操作符都是使用 BIGINT 算法。这意味着，
  如果你将两个大整数相乘（或者是函数的返回结果），当结果大于
  9223372036854775807 时，你可能会得到意想不到的结果。

### DECIMAL[(M[,D])] [UNSIGNED] [ZEROFILL]

一个包装好的“精确”的定点数。

M 是数字的总数（精度），小数点和负数的 `-` 符号不算在 M 中。
DECIMAL 的最大位数（M）是 65，如果 M 被省略，默认值是 10。

D 是小数点后的位数（刻度）。如果 D 是 0，那么值没有小数点或小数部分。
支持的小数（D）的最大数目是 30。如果省略了 D，默认值是 0。

UNSIGNED，如果指定，不允许负值。

DECIMAL 列的所有基本计算（+、-、*、/）都以 65 位的精度完成。

### DEC[(M[,D])] [UNSIGNED] [ZEROFILL], NUMERIC[(M[,D])] [UNSIGNED] [ZEROFILL], FIXED[(M[,D])] [UNSIGNED] [ZEROFILL]

这些类型都是 `DECIMAL` 的同义词。`FIXED` 的同义词可用来与其他数据库系统兼容。

### FLOAT[(M,D)] [UNSIGNED] [ZEROFILL]

一个小的（单精度的）浮点数。允许的值是-3.402823466 E+38 到 -1.175494351E-38，0，
和 1.175494351e-38 到 3.402823466 E+38。这些都是基于 IEEE 标准的理论限制。根据您
的硬件或操作系统，实际的范围可能会稍微小一些。

M 是数字的总数，D 是小数点后面的位数。如果省略了 M 和 D，那么值就会被存储为硬件
允许的范围内。单精度浮点数精确到大约小数点后 7 位。

UNSIGNED，如果指定，不允许负值。

使用 FLOAT 可能会给您带来一些意想不到的问题，因为 MySQL 中的所有计算都是双精度的
。see Section B.5.4.7, “Solving Problems with No Matching Rows”.

### DOUBLE[(M,D)] [UNSIGNED] [ZEROFILL]

一个正常大小（双精度）浮点数。允许的值是 -1.7976931348623157E+308 到
-2.2250738585072014E-308，0，和2.2250738585072014E-308 到
1.7976931348623157E+308。这些都是基于 IEEE 标准的理论限制。根据您的硬件或操作系
统，实际的范围可能会稍微小一些。

M 是数字的总数，D 是小数点后面的位数。如果省略了 M 和 D，那么值就会被存储到硬件
允许的范围内。双精度浮点数精确到小数点后 15 位。

UNSIGNED，如果指定，不允许负值。

### DOUBLE PRECISION[(M,D)] [UNSIGNED] [ZEROFILL], REAL[(M,D)] [UNSIGNED] [ZEROFILL]

这些类型都是 DOUBLE 的同义词。例外：如果启用了 `REAL_AS_FLOAT` SQL 模式，`REAL`
是 FLOAT 的同义词而不是 DOUBLE 的同义词。

### FLOAT(p) [UNSIGNED] [ZEROFILL]

一个浮点数。

p 代表以位为单位的精度，但是 MySQL 使用这个值只用来决定是否为结果的数据类型使用
FLOAT 或 DOUBLE。如果 p 是从 0 到 24，那么数据类型就会变成没有 M 或 D 值的浮点数
。如果 p 从 25 到 53，那么数据类型就变成了没有 M 或 D 值的 DOUBLE。结果列的范围
与本节前面描述的单精度 FLOAT 或双精度 DOUBLE 数据类型相同。

FLOAT(p) 语法是为 ODBC 兼容性提供的。

## 11.1.2 Date and Time Type Overview

下面是对时态数据类型的总结。有关时态类型的属性和存储需求的附加信息，请参阅
Section 11.3, “Date and Time Types”，以及 Section 11.8, “Data Type Storage
Requirements”。关于作用于时间值的函数的描述，参见 Section 12.7, “Date and Time
Functions”。

对于 DATE 和 DATETIME 范围描述，“supported 支持”意味着尽管早期的值可能起作用，
但不能保证。

MySQL 允许 TIME、DATETIME、和 TIMESTAMP 值的分数秒，最高可达微秒（6位）的精度。
要定义一个包含分数秒部分的列，使用语法 `type_name(fsp)`，其中 type_name 是TIME、
DATETIME、和 TIMESTAMP，而 fsp 是分秒精度。例如:
```
CREATE TABLE t1 (t TIME(3), dt DATETIME(6));
```

如果给定，fsp 值必须在0到6之间。0值表示没有小数部分。如果省略，默认的精度是0。（
这与标准的 SQL 默认值 6 不同，是为了与之前的 MySQL 版本兼容。）

表里的任何 TIMESTAMP 或 DATETIME 列都有自动初始化属性和自动更新属性

### DATE

一个日期。

所支持的范围从 `1000-01-01` 到 `9999-12-31`。MySQL 以 `YYYY-MM-DD` 的格式显示日
期值，但是允许使用字符串或数字将值赋值给 `DATE` 列。

### DATETIME[(fsp)]

一个日期和时间的组合。

支持的范围从 `1000-01-01:00:00:00.000000` 到 `9999-12-31 23:59:59.999999`。MySQL
以 `YYYY-MM-DD HH:MM:SS.[.fraction]` 格式显示 `DATETIME` 值，但允许使用字符串或
数字将值赋值给 `DATETIME` 列。

可选的 fsp 值范围从 0 到 6，用来指定分数秒精度。0 值表示没有小数部分。如果省略，
默认的精度是 0。

可以用列定义子句 `DEFAULT` 和 `ON UPDATE` 来分别指定 `DATETIME` 列自动初始化和自
动更新到当前日期和时间，see Section 11.3.5,“Automatic Initialization and
Updating for TIMESTAMP and DATETIME”

### TIMESTAMP[(fsp)]

一个时间戳。

范围是 `1970-01-01:00:00:01.000000` UTC 到 `2038-01-19 03:14:07.999999` UTC。
`TIMESTAMP` 值保存的是自计算机纪元开始（`1970-01-01:00:00` UTC）的秒数。
`TIMESTAMP` 不能表示值 '1970-01-01 00:00:00'，因为那等价于计算机纪元的 0 秒，值
0 被表示为  `0000-00-00:00:00`，“零”时间戳值。

可选的 fsp 值范围从 0 到 6，用来指定分数秒精度。0 值表示没有小数部分。如果省略，
默认的精度是 0。

服务器处理 `TIMESTAMP` 定义的方式取决于系统变量
`explicit_defaults_for_timestamp` 的值 (see Section 5.1.7, “Server System
Variables”)：

* 如果启用了 `explicit_defaults_for_timestamp`，则不会为任何 `TIMESTAMP` 列自动
  赋值属性 `DEFAULT CURRENT_TIMESTAMP` 或 `ON UPDATE CURRENT_TIMESTAMP`，这些属
  性必须被显式的包含在列定义中，同时，任何没有显式声明 `NOT NULL` 的 `TIMESTAMP`
  列都允许 `NULL` 值。

* 如果禁用了 `explicit_defaults_for_timestamp`，服务器按照如下方式处理
  `TIMESTAMP`：除非另有说明，如果没有显式地分配一个值，表中的第一个 `TIMESTAMP`
  列被定义为自动设置为最近修改的日期和时间。这使得 `TIMESTAMP` 对记录 `INSERT`
  或 `UPDATE` 操作的时间戳非常有用。你也可以通过给它赋值一个 `NULL` 值来设置任何
  `TIMESTAMP` 列为当前的日期和时间，除非它定义时使用了 `NULL` 属性，以允许
  `NULL` 值。

* 自动初始化和更新到当前日期和时间可以使用列定义子句 `DEFAULT CURRENT_TIMESTAMP`
  和 `ON UPDATE CURRENT_TIMESTAMP` 来指定。默认情况下，第一个 `TIMESTAMP` 列有如
  前所述的属性，然而，表中的任何 `TIMESTAMP` 列都可以被定义为具有这些属性。

### TIME[(fsp)]

一个时间。

范围是 '-838:59:59.000000' 到 '838:59:59.000000'，MySQL 以 'HH:MM:SS[.fraction]'
的格式显式 `TIME` 值，但是允许用字符串或数字为 `TIME` 列赋值。

可选的 fsp 值范围从 0 到 6，用来指定分数秒精度。0 值表示没有小数部分。如果省略，
默认的精度是 0。

### YEAR[(4)]

年是以四位数的格式。MySQL 以 `YYYY` 格式显示 `YEAR` 值，但允许使用字符串或数字将
值赋值给 `YEAR` 列。值显式为 1901 到 2155 和 0000。

关于 `YEAR` 显示格式和输入值的解释的附加信息，参考：Section 11.3.3, “The YEAR
Type”.

    注意
    MySQL 8.0 不支持 YEAR(2) 数据类型，老版本的 MySQL 支持。关于转换为 YEAR(4)
    的说明，参考：Section 11.3.4,“Migrating YEAR(2) Columns to YEAR(4)”.

聚合函数 `SUM()` 和 `AVG()` 不适用于时间值(它们将值转换为数字，丢弃了第一个非数
字字符之后的所有字符)。要解决这个问题，转换为数字单元，执行聚合操作，并转换回一
个时间值。例子：
```
SELECT SEC_TO_TIME(SUM(TIME_TO_SEC(time_col))) FROM tbl_name;
SELECT FROM_DAYS(SUM(TO_DAYS(date_col))) FROM tbl_name;
```

## 11.1.3 String Type Overview

以下是字符串数据类型的总结。有关字符串类型的属性和存储需求的附加信息，see
Section 11.4, “String Types”, and Section 11.8, “Data Type Storage
Requirements”.

在某些情况下，MySQL 可能会将字符串列更改为与 `CREATE TABLE` 或 `ALTER TABLE` 语
句中不同的类型。See Section 13.1.18.7, “Silent Column Specification Changes”.

MySQL 解释字符列定义中的长度规格以字符为单位。这适用于 `CHAR`、`VARCHAR` 和
`TEXT` 类型。

许多字符串数据类型的列定义可以指定列的字符集或校对规则属性。这些属性适用于 CHAR
、VARCHAR、TEXT 类型、ENUM 和 SET 数据类型：

* `CHARACTER SET` 属性制定了字符集，`COLLATE` 属性执行了校对规则，例如：
```
CREATE TABLE t
(
    c1 VARCHAR(20) CHARACTER SET utf8,
    c2 TEXT CHARACTER SET latin1 COLLATE latin1_general_cs
);
```
这个表定义创建了一个名为 c1 的列，字符集为 utf8 校对规则为此字符集的默认校对规则
，列 c2 字符集为 latin1，有一个区分大小写的校对规则。

当 `CHARACTER SET` 和 `COLLATE` 属性同时缺失或或缺失某个时，字符集和校对规则的赋
值规则描述参考: Section 10.3.5, “Column Character Set and Collation”.

`CHARSET` 是 `CHARACTER SET` 的同义词。

* 为字符字符串（a character string）数据类型指定 `CHARACTER SET` 的二进制属性会
  使列被创建为相应的二进制字符串（binary string）数据类型： `CHAR` 变成 `BINARY`
  ，`VARCHAR` 变成 `VARBINARY`，`TEXT` 变成 `BLOB`。但对于 `ENUM` 和 `SET` 数据
  类型则不会这样，它们是按照声明进行创建。假设您使用下列定义指定了一个表：

```
CREATE TABLE t
(
    c1 VARCHAR(10) CHARACTER SET binary,
    c2 TEXT CHARACTER SET binary,
    c3 ENUM('a','b','c') CHARACTER SET binary
);
```

结果表的定义变为：
```
CREATE TABLE t
(
    c1 VARBINARY(10),
    c2 BLOB,
    c3 ENUM('a','b','c') CHARACTER SET binary
);
```

* `BINARY` 属性是指定表默认字符集和该字符集的二进制（`_bin`）校对规则的简写。在
  这种情况下，比较和排序是基于字符的数字代码值。

* `ASCII` 属性是 `CHARACTER SET latin1` 的简写

* `UNICODE` 属性是 `CHARACTER SET ucs2` 的简写

字符列的比较和排序是基于分配给列的校对规则。对于 `CHAR`、`VARCHAR`、`TEXT`、
`ENUM` 和 `SET` 数据类型，你可以用二进制（`_bin`）校对规则或 `BINARY` 属性声明一
个列，以使得比较和排序时使用底层的字符代码值而不是词汇排序。

有关在 MySQL 中使用字符集的附加信息，请参阅 Chapter 10, Character Sets,
Collations, Unicode.

* [NATIONAL] CHAR[(M)] [CHARACTER SET charset_name] [COLLATE collation_name]

一种固定长度的字符串

它总是在存储的时候把右侧用空格填充到指定的长度。M 表示按字符计算的列长度。M 的范
围是 0 到 255。如果 M 被省略了，长度是 1。

    注意：
    除非启用了 `PAD_CHAR_TO_FULL_LENGTH` SQL 模式，否则当检索 CHAR 值时，尾部的
    空格总是被移除。

`CHAR` 是 `CHARACTER` 的缩写，`NATIONAL CHAR` (或者它的等效短格式 `NCHAR`) 是标
准 SQL 定义一些儿使用预定义字符集的 `CHAR` 列的方法，MySQL 使用 `utf8` 作为预定
义的字符集。see Section 10.3.7, “The National Character Set”.

`CHAR BYTE` 数据类型是 `BINARY` 数据类型的别名，这是一个兼容性特性。

MySQL 允许您创建类型为 `CHAR(0)` 的列。这在很大程度上是有用的，当您必须使用旧应
用程序，而旧应用程序又依赖于列存在但实际上并不使用列的值时。当您需要一个只能取两
个值的列时，`CHAR(0)` 也很不错，：一个被定义为 `CHAR(0) NULL` 的列只占用一个位，
并且只能取值为 `NULL` 和 `''`（空字符串）。

* [NATIONAL] VARCHAR(M) [CHARACTER SET charset_name] [COLLATE collation_name]

一个长度可变的字符串

M represents the maximum column length in characters. The range of M is 0 to
65,535. The effective maximum length of a VARCHAR is subject to the maximum row
size (65,535 bytes, which is shared among all columns) and the character set
used. For example, utf8 characters can require up to three bytes per character,
so a VARCHAR column that uses the utf8 character set can be declared to be a
maximum of 21,844 characters. 
M 表示列的最大长度，按字符计算。M 的范围是 0 到 65,535（字符？）。VARCHAR 的有效
最大长度取决于列长度最大的那行的大小（65,535 字节（？），在所有列中共享）和所使
用的字符集。例如，utf8 字符可以要求每个字符最多 3 个字节，因此使用 utf8 字符集的
VARCHAR 列最多可以被声明为 21844 个字符。请参阅 Section C.10.4, “Limits on
Table Column Count and Row Size”
（？？？为什么不是 21845 个字符，0 到 65535，按 3 个字节算一个字符）
（前后两个 65535，到底是字节还是字符）

MySQL stores VARCHAR values as a 1-byte or 2-byte length prefix plus data. The
length prefix indicates the number of bytes in the value. A VARCHAR column uses
one length byte if values require no more than 255 bytes, two length bytes if
values may require more than 255 bytes.
MySQL 存储 VARCHAR 值为 1 字节或 2 字节长度前缀加数据。长度前缀表示值中的字节数
。如果值不超过 255 字节，则 VARCHAR 列使用一个长度字节，如果值可能需要超过 255
字节，则需要两个长度字节。

    注意：
    MySQL 遵循标准的 SQL 规范，不从 VARCHAR 值中删除尾随空格。

`VARCHAR` 是 `CHARACTER VARYING` 的缩写，`NATIONAL VARCHAR` 是标准 SQL 定义
一些儿使用预定义字符集的 `VARCHAR` 列的方法，MySQL 使用 `utf8` 作为预定义的字符
集。see Section 10.3.7, “The National Character Set”.

* BINARY[(M)]

`BINARY` 类型类似于 `CHAR` 类型，但是存储二进制的字节字符串而不是非二进制的字符
字符串。可选的长度 M 表示按字节计算的列长度。如果省略，M 默认为 1。

* VARBINARY(M)

`VARBINARY` 类型类似于 `VARCHAR` 类型，但是存储二进制的字节字符串而不是非二进制的字符
字符串。长度 M 表示按字节计算的最大列长度。

* TINYBLOB

一个最大长度为 255 (2^8 - 1) 字节的 BLOB 列。每一个 TINYBLOB 值存储时都使用一个
1 字节长度的前缀来表明值中的字节数。

* TINYTEXT [CHARACTER SET charset_name] [COLLATE collation_name]

一个最大长度为 255（2^8 - 1）字符的 TEXT 列。如果值包含多字节字符，那么有效的最大
长度就更少了。每一个 TINYTEXT 值存储时都使用一个 1 字节长度的前缀来表明值中的字
节数。

* BLOB[(M)]

An optional length M can be given for this type. If this is done, MySQL creates
the column as the smallest BLOB type large enough to hold values M bytes long.
一个最大长度为 65,535（2^16 - 1）字节的 BLOB 列。每一个 BLOB 值存储时都使用一个
2 字节长度的前缀来表示值中的字节数。长度 M 可选。如果给了 M 值，MySQL 就会创建能
容纳 M 个字节值的最小 BLOB 类型列。

* TEXT[(M)] [CHARACTER SET charset_name] [COLLATE collation_name]

一个最大长度为 65,535（2^16-1）字符的 TEXT 列。如果值包含多字节字符，那么有效的
最大长度就更少了。每一个 TEXT 值存储时都使用一个 2 字节长度的前缀来表示值中的字
节数。长度 M 可选。如果给了 M 值，MySQL 就会创建能容纳 M 个字符值的最小 TEXT 类
型列。

* MEDIUMBLOB

一个最大长度为 16,777,215（2^24-1）字节的 BLOB 列。每一个 MEDIUMBLOB  值存储时都
使用一个 3 字节长度的前缀来表示值中的字节数。

* MEDIUMTEXT [CHARACTER SET charset_name] [COLLATE collation_name]

一个最大长度为 16,777,215（2^24-1）字符的 TEXT 列。如果值包含多字节字符，那么有
效的最大长度就更少了。每一个中文本值存储时都使用一个 3 字节长度的前缀来表示值中
的字节数。

* LONGBLOB

一个最大长度为 4,294,967,295 或 4 GB（2^32-1）字节的 BLOB 列。LONGBLOB 列的有效
最大长度取决于在客户端/服务器协议和可用内存中配置的最大数据包大小。每一个
LONGBLOB 值存储时都使用一个 4 字节长度的前缀来表示值中的字节数。

* LONGTEXT [CHARACTER SET charset_name] [COLLATE collation_name]

一个最大长度为 4,294,967,295 或 4 GB（2^32-1）字符的 TEXT 列。如果值包含多字节字
符，那么有效的最大长度就更少了。LONGTEXT 列的有效最大长度也取决于在客户端/服务器
协议和可用内存中配置的最大数据包大小。每一个 LONGTEXT 值存储时都使用一个 4 字节
长度的前缀来表示值中的字节数。

* ENUM('value1','value2',...) [CHARACTER SET charset_name] [COLLATE collation_name]

一个枚举
一个字符串对象，只能有一个值，值从列表 'value1', 'value2', ..., NULL 或特殊的错
误值 '' 中选择。ENUM 的值在内部表示为整数，一个 ENUM 列最多可包含 65,535 个不同
的元素，每个单独的 ENUM 元素最大长度是 `M <= 255 and (M x w) <= 1020`，M 是元素
的文字长度，w 是字符集中的最大长度字符需要的字节数。

* SET('value1','value2',...) [CHARACTER SET charset_name] [COLLATE collation_name]

一个集合

一个字符串对象，可以有 0 个或多个值，每个值必须从列表 'value1', 'value2', ... 里
选择。SET 值在内部表示为整数。SET 列最多可以有 64 个不同的成员，每个 SET 元素受
支持的最大长度是 `M <= 255 and (M x w) <= 1020`，M 是元素的文字长度，w 是字符集
中的最大长度字符需要的字节数。

