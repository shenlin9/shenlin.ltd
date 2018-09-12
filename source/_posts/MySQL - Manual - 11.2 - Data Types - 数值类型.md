---
title: MySQL - Manual - 11.2 - Data Types - 数值类型
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 数值类型

<!--more-->

MySQL 支持所有标准的 SQL 数值数据类型。这些类型包括精确的数值数据类型（INTEGER、
SMALLINT、DECIMAL 和 NUMERIC），以及近似的数值数据类型（FLOAT、REAL 和 DOUBLE
PRECISION）。关键字 INT 是 INTEGER 的同义词，而关键字 DEC 和 FIXED 是 DECIMAL
的同义词。MySQL 将 DOUBLE 作为 DOUBLE PRECISION 的同义词（非标准扩展）。MySQL 还
将 REAL 视为 DOUBLE PRECISION（非标准变体）的同义词，除非启用了 `REAL_AS_FLOAT`
SQL 模式。

`BIT` 数据类型存储比特值，并支持 MyISAM、MEMORY 和 InnoDB。

关于 MySQL 如何处理在表达式评估期间将超出范围的值分配给列和溢出的信息，see
Section 11.2.6, “Out-of-Range and Overflow Handling”.

有关数值类型存储需求的信息，see Section 11.8, “Data Type Storage Requirements”

操作数是数值的计算结果的数据类型取决于操作数的类型和在它们上执行的操作。有关更多
信息，see Section 12.6.1, “Arithmetic Operators”.

## 11.2.1 Integer Types (Exact Value) - INTEGER, INT, SMALLINT, TINYINT, MEDIUMINT, BIGINT 

MySQL 支持 SQL 标准整数类型 INTEGER（或 INT）和 SMALLINT。作为标准的扩展，MySQL
还支持整数类型 TINYINT、MEDIUMINT 和 BIGINT。下表显示了每个整数类型所需的存储和
范围。

**MySQL支持的整数类型的存储和范围**

    类型    存储字节 有符号最小值    有符号最大值  无符号最小值  无符号最大值
    TINYINT     1       -128           127             0            255
    SMALLINT    2       -32768         32767           0            65535
    MEDIUMINT   3       -8388608       8388607         0            16777215
    INT         4       -2147483648    2147483647      0            4294967295
    BIGINT      8       -2^63          2^63-1          0            2^64-1

## 11.2.2 Fixed-Point Types (Exact Value) - DECIMAL, NUMERIC

precision:精度 scale:刻度

DECIMAL 和 NUMERIC 类型存储精确的数值数据值。当保持精确的精确度很重要时，就使用
这些类型，例如货币数据。在 MySQL 中，NUMERIC 被实现为 DECIMAL，所以下面的关于
DECIMAL 的说明同样适用于 NUMERIC。

MySQL 以二进制格式存储 DECIMAL 值。See Section 12.23, “Precision Math”.

在 DECIMAL 列声明中，通常是指定精度和刻度的，例如:
```
salary DECIMAL(5,2)
```
在这个例子中，5 是精度，2 是刻度。精度代表了存储为值的有效数字的数量，而刻度表示
可以在小数点后存储的数字的数量。

标准 SQL 要求 DECIMAL(5,2) 能够以 5 位数字和两个小数存储任何值，因此可以存储在列
范围内的值从 -999.99 到 999.99。

在标准 SQL 中，语法 DECIMAL(M) 等价于 DECIMAL(M,0)。类似地，语法 DECIMAL 等价于
DECIMAL(M,0)，允许由实现来确定 M 的值。MySQL 支持这两种不同形式的 DECIMAL 语法，
M 的默认值是 10。

如果刻度是 0，则 DECIMAL 值不包含小数点或小数部分。

DECIMAL 的最大位数是 65，但是一个给定的 DECIMAL 列的实际范围可以被精度或刻度约束
，但这样的一个列被赋值时，若小数点后的位数多于刻度指定的位数，则值被转换到指定的
刻度。（精确的行为是特定于操作系统的，但通常效果是截断到允许的位数。）

## 11.2.3 Floating-Point Types (Approximate Value) - FLOAT, DOUBLE

FLOAT 和 DOUBLE 类型表示近似的数值数据值。MySQL 使用 4 个字节来表示单精度值，8
个字节用于双精度值。

对于 `FLOAT` ，SQL 标准允许 `FLOAT` 关键字后面的括号里有一个可选的按位计算的精度
规范（而不是指数的范围）。MySQL 也支持这个可选的精度规范，但是精度值只用于确定存
储大小。从 0 到 23 的精度可以得到一个 4 字节的单精度 `FLOAT` 列。从 24 到 53 的
精度可以得到一个 8 字节的双精度 `DOUBLE` 列。

MySQL 允许非标准的语法： `FLOAT(M,D)` or `REAL(M,D)` or `DOUBLE PRECISION(M,D)`
。这里 `(M,D)`的意思是，可以存储的值位数最多为 M 位，其中 D 位数可能在小数点后。
例如，一个定义为 `FLOAT(7,4)` 的列在显示时将会看起来像 `-999.9999`。MySQL 在存储
值时执行四舍五入，所以如果你将 `999.00009` 插入到 `FLOAT(7,4)` 列中，近似的结果
是 `999.0001`。

因为浮点值是近似的，而不是作为精确的值存储的，所以试图在比较中精确地对待它们可能
会导致问题。它们还受制于平台或实现依赖关系。更多信息参考：Section B.5.4.8, “
Problems with Floating-Point Values”

为了获得最大的可移植性，需要存储近似数值数据值的代码应该使用不带精确规范或位数的
`FLOAT` 或 `DOUBLE PRECISION`。

## 11.2.4 Bit-Value Type - BIT

BIT 数据类型用来存储位值。BIT(M) 允许存储 M 位值。M 的范围从1到64。

要指定位值，可以使用 `b'value'` 表示法。`value` 是用 0 和 1 写成的二进制值。例如
，`b'111'` 和 `b'10000000'` 分别代表 7 和 128。参见 Section 9.1.5, “Bit-Value
Literals”

如果你为列 `BIT(M)` 赋的值少于 M 位长，则值的左边会被填充上 0。例如：为列
`BIT(6)` 赋值 `b'101'` 则实际上是赋值 `b'000101'`

## 11.2.5 Numeric Type Attributes

MySQL 支持一个扩展，可在类型的基本关键字后面的括号里指定整数类型的显示宽度（可选）
。例如，INT(4) 指定一个显示宽度为 4 位的 INT。这个可选的显示宽度可以被应用程序用
来显示整型值，若整型值的宽度小于为列指定的宽度，则左侧用空格填充。（也就是说，这
个宽度存在于和结果集一起返回的元数据中。它是否被使用取决于应用程序。）

显示宽度并不限制可以存储在列中的值的范围。它也不能阻止比显示宽度更宽的值被正确显
示。例如，指定为 `SMALLINT(3)` 的列的数值范围是通常的 `SMALLINT` 范围 -32768 到
32767，但在 3 位数范围之外的值也被全部显示。

当与非标准的可选属性 `ZEROFILL` 一起使用时，默认填充的空格被替换为 0。例如，对于
一个声明为 `INT(4) ZEROFILL` 的列， 值 5 被检索为 `0005`。

    注意：
    当列涉及到表达式或 UNION 查询时，ZEROFILL 属性将被忽略。

    如果你在一个有 ZEROFILL 属性的整数列中存储大于显示宽度的值，那么当 MySQL 为
    一些复杂的连接生成临时表时，可能会遇到问题。在这些情况下，MySQL 假设数据值符
    合列显示宽度。

所有整数类型都可以有一个可选的（非标准的）属性 `UNSIGNED`。无符号类型用于只允许
列中有非负数的数字，或者当您需要列有一个更大的数值范围上限时。例如，如果一个
`INT` 列是 `UNSIGNED`，那么列的范围大小是相同的，但是它的端点从 -2147483648 和
2147483647 转为 0 和 4294967295。

浮点型和定点类型也可以是 `UNSIGNED` 的。与整型类型一样，该属性可以防止负值被储存
在列中。与整数类型不同的是，列值的上限保持不变。

如果你为一个数值列指定 `ZEROFILL`，MySQL 会自动将 `UNSIGNED` 属性添加到此列。

整数或浮点数据类型可以有附加属性的 `AUTO_INCREMENT`。当你将一个 `NULL` 值插入到
一个索引的 `AUTO_INCREMENT` 列中时，这个列被赋值为下一个序列值，通常是 `value+1`
，其中 `value` 是表中当前列的最大值。（`AUTO_INCREMENT` 序列从 1 开始。）

保存 0 到一个 `AUTO_INCREMENT` 列和保存 `NULL` 有同样的效果，除非启用了
`NO_AUTO_VALUE_ON_ZERO` SQL 模式。

插入 `NULL` 来生成 `AUTO_INCREMENT` 值，要求此列被声明为 `NOT NULL` 。如果此列被
声明为 `NULL` ，则插入 `NULL` 则存储 `NULL` 。当你将任何其他值插入到
`AUTO_INCREMENT` 列中时，列就被设置为那个值，且序列被重置，所以下一个自动生成的
值按顺序从插入的值开始。

MySQL 8.0，`AUTO_INCREMENT` 列不支持负值。

## 11.2.6 Out-of-Range and Overflow Handling

当 MySQL 在数字列中存储一个列数据类型允许范围之外的值时，结果取决于当时的 SQL 模
式：

* 如果启用了严格的 SQL 模式，MySQL 会拒绝超出范围的值并报错，插入失败，这也和
  SQL 标准一致。

* 如果没有启用限制性的模式，MySQL 将该值裁剪到列数据类型范围的适当端点，并存储由
  此产生的值。

  当一个超出范围的值被分配给一个整数列时，MySQL 存储代表列数据类型范围的相应端点
  的值。

  当一个浮点数或定点列被赋值超过指定（或默认）精度和刻度所暗示的范围时，MySQL 存
  储代表该范围相应端点的值。

假如表 t1 定义：
```
CREATE TABLE t1 (i1 TINYINT, i2 TINYINT UNSIGNED);
```

启用严格 SQL 模式，则发生 out of range 错误：
```
mysql> SET sql_mode = 'TRADITIONAL';

mysql> INSERT INTO t1 (i1, i2) VALUES(256, 256);
ERROR 1264 (22003): Out of range value for column 'i1' at row 1

mysql> SELECT * FROM t1;
Empty set (0.00 sec)
```

不启用严格 SQL 模式，则裁剪值并警告：
```
mysql> SET sql_mode = '';

mysql> INSERT INTO t1 (i1, i2) VALUES(256, 256);

mysql> SHOW WARNINGS;
+---------+------+---------------------------------------------+
| Level   | Code | Message                                     |
+---------+------+---------------------------------------------+
| Warning | 1264 | Out of range value for column 'i1' at row 1 |
| Warning | 1264 | Out of range value for column 'i2' at row 1 |
+---------+------+---------------------------------------------+

mysql> SELECT * FROM t1;
+------+------+
| i1  | i2  |
+------+------+
| 127 | 255 |
+------+------+
```

当没有启用严格 SQL 模式时，由于数据裁剪而产生的列赋值转换被报告为 `ALTER TABLE`
、`LOAD DATA INFILE`、`UPDATE` 和多行 `INSERT` 语句的警告。在严格的模式下，这些
语句失败了，一些或所有的值都没有插入或更改，这取决于表是否是事务性表和其他因素。
有关详细信息，请参阅 Section 5.1.10,“Server SQL Modes”

在数值表达式中，溢出会导致错误。例如，最大的 `BIGINT` 值是 `9223372036854775807`
，因此下面的表达式会产生一个错误：
```
mysql> SELECT 9223372036854775807 + 1;
ERROR 1690 (22003): BIGINT value is out of range in '(9223372036854775807 + 1)'
```

为了使操作在这种情况下成功，将值转换为无符号;
```
mysql> SELECT CAST(9223372036854775807 AS UNSIGNED) + 1;
+-------------------------------------------+
| CAST(9223372036854775807 AS UNSIGNED) + 1 |
+-------------------------------------------+
| 9223372036854775808 |
+-------------------------------------------+
```

溢出是否发生取决于操作数的范围，所以处理前面表达式的另一种方法是使用精确值算法，
因为 `DECIMAL` 值的范围比整数大：
```
mysql> SELECT 9223372036854775807.0 + 1;
+---------------------------+
| 9223372036854775807.0 + 1 |
+---------------------------+
| 9223372036854775808.0     |
+---------------------------+
```

整型值之间的减法，其中一个是 `UNSIGNED` 时，默认情况下会产生一个无符号结果。如果
结果是负值，则会出现错误：
```
mysql> SET sql_mode = '';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT CAST(0 AS UNSIGNED) - 1;
ERROR 1690 (22003): BIGINT UNSIGNED value is out of range in '(cast(0 as unsigned) - 1)'
```

如果启用了 `NO_UNSIGNED_SUBTRACTION` SQL 模式，则结果为负值时不出错：
```
mysql> SET sql_mode = 'NO_UNSIGNED_SUBTRACTION';
mysql> SELECT CAST(0 AS UNSIGNED) - 1;
+-------------------------+
| CAST(0 AS UNSIGNED) - 1 |
+-------------------------+
| -1 |
+-------------------------+
```
如果这样操作的结果用来更新一个 `UNSIGNED` 整数列，则结果被裁剪为列类型的最大值，
或者裁剪为 0 如果启用了 `NO_UNSIGNED_SUBTRACTION` SQL 模式。如果启用了严格 SQL
模式，则产生错误列不会更改。
