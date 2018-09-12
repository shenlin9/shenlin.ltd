---
title: MySQL - Manual - 10.8 - 字符集：校对规则问题 - Collation Issues
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 字符集：校对规则问题

<!--more-->

下面的部分将讨论字符集校对规则的各个方面。

## 10.8.1 Using COLLATE in SQL Statements

通过 `COLLATE` 子句，可以覆盖任何默认的校对规则来进行比较。`COLLATE` 可以在 SQL
语句的各个部分中使用。下面是一些例子:

* 搭配 `ORDER BY`:
```
SELECT k
FROM t1
ORDER BY k COLLATE latin1_german2_ci;
```

* 搭配 `AS`:
```
SELECT k COLLATE latin1_german2_ci AS k1
FROM t1
ORDER BY k1;
```

* 搭配 `GROUP BY`:
```
SELECT k
FROM t1
GROUP BY k COLLATE latin1_german2_ci;
```

* 搭配 聚合函数:
```
SELECT MAX(k COLLATE latin1_german2_ci)
FROM t1;
```

* 搭配 `DISTINCT`:
```
SELECT DISTINCT k COLLATE latin1_german2_ci
FROM t1;
```

* 搭配 `WHERE`:
```
SELECT *
FROM t1
WHERE _latin1 'Müller' COLLATE latin1_german2_ci = k;

SELECT *
FROM t1
WHERE k LIKE _latin1 'Müller' COLLATE latin1_german2_ci;
```

* 搭配 `HAVING`:
```
SELECT k
FROM t1
GROUP BY k
HAVING k = _latin1 'Müller' COLLATE latin1_german2_ci;
```

## 10.8.2 COLLATE Clause Precedence

`COLLATE` 子句具有较高的优先级（高于 `||`），因此下面两个表达式是等价的：
```
x || y COLLATE z
x || (y COLLATE z)
```

## 10.8.3 Character Set and Collation Compatibility

每一个字符集都有一个或多个校对规则，但是每一个校对规则都只和唯一一个字符集相关联
，因此，下面的语句会导致一条错误消息，因为 `latin2bin` 校对规则与 `latin1` 字符
集不合法：
```
mysql> SELECT _latin1 'x' COLLATE latin2_bin;
ERROR 1253 (42000): COLLATION 'latin2_bin' is not valid
for CHARACTER SET 'latin1'
```

## 10.8.4 Collation Coercibility in Expressions

绝大多数的语句中，都可以很明显看出 MySQL 进行比较操作时使用的是什么校对规则。例
如，下例种应该清楚地看出是使用列 `x` 的校对规则：
```
SELECT x FROM T ORDER BY x;
SELECT x FROM T WHERE x = x;
SELECT DISTINCT x FROM T;
```
然而，有多个操作数时，可能会有歧义。例如:
```
SELECT x FROM T WHERE x = 'Y';
```
比较时应该使用列 `x` 的校对规则，还是用字符串字面量 `'Y'` 的校对规则？`x` 和
`'Y'` 都有校对规则，哪个优先呢？


校对规则的混合还可能发生在不是比较的上下文中，例如，一个多参数的连接操作
`CONCAT(x,'Y')`，将多个参数组合生成一个单独的字符串，生成结果的校对规则是什么？

为了解决这类问题，MySQL 检查某项的校对规则是否可以被强制为另一项的校对规则。
MySQL 将强制值赋值如下：

* 一个明确的 `COLLATE` 子句强制性为 0（根本不强制）。
* 使用不同校对规则的两个字符串的连接强制性为 1
* 列、存储例程参数、本地变量的校对规则的强制性为 2
* 一个“系统常量”（函数如 USER() 或 VERSION() 返回的字符串 ）的强制性为 3
* 字面量校对规则的强制性为 4
* 数字或时间值的校对规则的强制性为 5
* NULL 或从 NULL 派生出的表达式的强制性为 6

MySQL使用强制值和以下规则来解决歧义：

* 使用具有最低强制值的校对规则。
* 如果两边有相同的强制值，则：
    * 如果两边都是 Unicode，或者两边都不是 Unicode，则是一个错误。
    * 如果一边为 Unicode 字符集，而另一边为非 Unicode 字符集，则使用 Unicode 字
      符集的那边获胜，自动字符集转换应用于非 Unicode 那边。例如，下面的语句不会
      返回一个错误：
      ```
      SELECT CONCAT(utf8_column, latin1_column) FROM t1;
      ```
      返回结果使用的是 utf8 字符集，和列 `utf8_column` 字符集相同，列
      `latin1_column` 的值在连接前被自动转换为 utf8.
    * 如果一个操作的操作数使用相同的字符集，但是混合了一个 `_bin` 校对规则和一个
      `_ci` 或 `_cs` 校对规则，则使用 `_bin` 校对规则。这类似于将非二进制和二进
      制字符串混合在一起的操作，将操作数作为二进制字符串来评估，只不过它是用于校
      对规则而不是数据类型。

尽管自动转换不在 SQL 标准中，但是标准确实说每个字符集都是（在受支持的字符方面）
 Unicode 的“子集”。因为这是一个众所周知的原则：“适用于超集的东西可以应用于一
 个子集”，我们相信一个 Unicode 的校对规则可以应用于非 Unicode 字符串的比较。

下表说明了前面规则的一些应用：

    比较                                使用的校对规则
    column1 = 'A'                           使用 column1 的校对规则
    column1 = 'A' COLLATE x                 使用 'A' COLLATE x 的校对规则
    column1 COLLATE x = 'A' COLLATE y       错误

要确定一个字符串表达式的强制性，使用函数 `COERCIBILITY()`，参考：Section 12.14,
“Information Functions”:
```
mysql> SELECT COERCIBILITY('A' COLLATE latin1_swedish_ci);
-> 0

mysql> SELECT COERCIBILITY(VERSION());
-> 3

mysql> SELECT COERCIBILITY('A');
-> 4

mysql> SELECT COERCIBILITY(1000);
-> 5
```

对于将数字或时间值隐式转换为字符串的情况，例如表达式 `CONCAT(1, 'abc')` 中参数
`1` 发生的情况，结果是一个字符（非二进制）字符串，字符集和校对规则由系统变量
`character_set_connection` 和 `collation_connection` 决定。

参考：Section 12.2, “Type Conversion in Expression Evaluation”.

## 10.8.5 The binary Collation Compared to _bin Collations

这一节描述了二进制字符串的 `binary` 校对规则如何与非二进制字符串的 `_bin` 校对规
则进行比较。

二进制字符串（使用 BINARY、VARBINARY 和 BLOB 数据类型存储）使用一个名为 `binary`
的字符集和校对规则。二进制字符串是字节序列，这些字节的数字值决定了比较和排序顺序
。

非二进制字符串（使用 CHAR、VARCHAR 和 TEXT 数据类型存储）除了 `binary` 之外，还
有一套字符集和校对规则。一个给定的非二进制字符集可以有几个校对规则，每一个校对规
则都为集合里的字符定义了一个特定的比较和排序顺序，其中一个是字符集的二进制校对规
则，校对规则名中由一个 `_bin` 后缀表示。例如，`latin1` 和 `utf8` 的二进制校对规
则分别被命名为 `latin1_bin` 和 `utf8_bin`。

`binary` 校对规则在几个方面不同于 `_bin` 校对规则：

**比较和排序的单位**

二进制字符串是字节序列。对于 `binary` 校对规则，比较和排序是基于字节的数字值的。
非二进制字符串是字符序列，可能是多字节的。非二进制字符串的校对规则定义了字符值比
较和排序时的顺序。对于 `_bin` 校对规则，这个顺序是基于字符代码数字值的，这类似于
二进制字符串的排序，只不过字符代码值可能是多字节的。

**字符集转换** 

一个非二进制字符串有一个字符集，并且在许多情况下会自动转换为另一个字符集，即使字
符串有一个 `_bin` 校对规则：

* 当从另一个使用不同字符集的列中分配列值时：
```
UPDATE t1 SET utf8_bin_column=latin1_column;
INSERT INTO t1 (latin1_column) SELECT utf8_bin_column FROM t2;
```

* 当使用字符串字面量为 `INSERT` 或 `UPDATE` 分配列值时：
```
SET NAMES latin1;
INSERT INTO t1 (utf8_bin_column) VALUES ('string-in-latin1');
```

* 当把结果从服务器端发送给客户端时：
```
SET NAMES latin1;
SELECT utf8_bin_column FROM t2;
```

对于二进制字符串列，不发生转换。对于前面的例子，字符串值是按字节来复制的。

**字母大小写转换**

非二进制字符集的校对规则提供了关于字符的字母大小写的信息，因此非二进制字符串中的
字符可以从一个字母大小写转换到另一个字母大小写，即使是排序时忽略字母大小写的
`_bin` 校对规则：
```
mysql> SET NAMES latin1 COLLATE latin1_bin;
mysql> SELECT LOWER('aA'), UPPER('zZ');
+-------------+-------------+
| LOWER('aA') | UPPER('zZ') |
+-------------+-------------+
| aa          | ZZ          |
+-------------+-------------+
```

字母大小写的概念不适用于二进制字符串中的字节。要执行字母大小写转换，必须将字符串
转换成非二进制字符串：
```
mysql> SET NAMES binary;
mysql> SELECT LOWER('aA'), LOWER(CONVERT('aA' USING latin1));
+-------------+-----------------------------------+
| LOWER('aA') | LOWER(CONVERT('aA' USING latin1)) |
+-------------+-----------------------------------+
| aA | aa |
+-------------+-----------------------------------+
```

**比较时尾部空白的处理**

大多数 MySQL 校对规则都有“PAD SPACE”的填充属性。例外的是基于 UCA 9.0 或更高版
本的 Unicode 校对规则，它们有一个“NO PAD”的填充属性（参考 Section 10.10.1, “
Unicode Character Sets”）。

要确定校对规则的填充属性，使用 `INFORMATION_SCHEMA COLLATIONS` 表的
`PAD_ATTRIBUTE` 列。

填充属性决定了如何处理尾随空格以比较非二进制字符串（CHAR、VARCHAR 和 TEXT 值）。
`NO PAD` 校对规则处理字符串末尾的空格像任何其他字符一样。对于 `PAD SPACE` 校对规
则，尾部空白在比较中是不重要的，字符串被比较时不考虑任何后面的空格：
```
mysql> SET NAMES utf8 COLLATE utf8_bin;

mysql> SELECT 'a ' = 'a';
+------------+
| 'a ' = 'a' |
+------------+
| 1          |
+------------+
```
对于二进制字符串，所有字符在比较中都很重要，包括后面的空格：
```
mysql> SET NAMES binary;
mysql> SELECT 'a ' = 'a';
+------------+
| 'a ' = 'a' |
+------------+
| 0 |
+------------+
```

**插入和检索时尾部空白的处理**

`CHAR(N)` 列存储非二进制字符串，少于 N 个字符的值在插入时使用空格扩展。检索数据
时，尾部空格被移除。

`BINARY(N)` 列存储二进制字符串，少于 N 个字节的值在插入时使用 `0x00` 字节扩展，
检索数据时，不移除任何东西。总是返回声明长度的值。
```
mysql> CREATE TABLE t1 (
    a CHAR(10) CHARACTER SET utf8 COLLATE utf8_bin,
    b BINARY(10)
);

mysql> INSERT INTO t1 VALUES ('a','a');
mysql> SELECT HEX(a), HEX(b) FROM t1;
+--------+----------------------+
| HEX(a) | HEX(b)               |
+--------+----------------------+
| 61     | 61000000000000000000 |
+--------+----------------------+
```

## 10.8.6 Examples of the Effect of Collation

**Example 1: Sorting German Umlauts**

假设表 `T` 的 `X` 列有下列 `latin1` 字符集的值：
```
Muffler
Müller
MX Systems
MySQL
```

再假设使用下列语句检索列值：
```
SELECT X FROM T ORDER BY X COLLATE collation_name;
```

下表展示了我们使用 `ORDER BY` 排序时，使用不同的字符集的排序：

    latin1_swedish_ci       latin1_german1_ci           latin1_german2_ci
    Muffler                 Muffler                     Müller
    MX Systems              Müller                     Muffler
    Müller                 MX Systems                  MX Systems
    MySQL                   MySQL                       MySQL

排序不同的原因是因为那个带有两个点的 U 字符，在德语里称为  “U-umlaut.”(U 元音
变音)：
* 第一列排序时使用 Swedish/Finnish 校对规则，它设定 U-umlaut 和 Y 一致
* 第二列排序时使用 German DIN-1 校对规则，它设定 U-umlaut 和 U 一致
* 第三列排序时使用 German DIN-2 校对规则，它设定 U-umlaut 和 UE 一致

**Example 2: Searching for German Umlauts**

假设你有 3 个表，仅仅使用的字符集和校对规则不同：
```
mysql> SET NAMES utf8;

mysql> CREATE TABLE german1 (
c CHAR(10)
) CHARACTER SET latin1 COLLATE latin1_german1_ci;

mysql> CREATE TABLE german2 (
c CHAR(10)
) CHARACTER SET latin1 COLLATE latin1_german2_ci;

mysql> CREATE TABLE germanutf8 (
c CHAR(10)
) CHARACTER SET utf8 COLLATE utf8_unicode_ci;
```

每个表包含两条记录：
```
mysql> INSERT INTO german1 VALUES ('Bar'), ('Bär');
mysql> INSERT INTO german2 VALUES ('Bar'), ('Bär');
mysql> INSERT INTO germanutf8 VALUES ('Bar'), ('Bär');
```

上面的校对规则中有两种是使得 `A = Ä` 成立的，而另一个则不是（latin1_german2_ci）
。因为这个原因，你会得到这些比较结果：
```
mysql> SELECT * FROM german1 WHERE c = 'Bär';
+------+
| c |
+------+
| Bar |
| Bär |
+------+

mysql> SELECT * FROM german2 WHERE c = 'Bär';
+------+
| c |
+------+
| Bär |
+------+

mysql> SELECT * FROM germanutf8 WHERE c = 'Bär';
+------+
| c |
+------+
| Bar |
| Bär |
+------+
```

这不是一个 bug，而是`latin1_german1_ci` 和 `utf8_unicode_ci` 排序属性的结果（显
示的排序是根据“German DIN 5007”标准进行的）。

## 10.8.7 Using Collation in INFORMATION_SCHEMA Searches

`INFORMATION_SCHEMA` 表里的字符串列使用 `utf8_general_ci` 校对规则, 不区分大小写
，然而，对于那些与文件系统中表示的对象相对应的值，如数据库和表，
`INFORMATION_SCHEMA` 字符串列中的搜索可以是区分大小写的，也可以是不区分大小写的
，这取决于底层文件系统的特征和 `lower_case_table_names` 系统变量设置。

例如，如果文件系统是区分大小写的，那么搜索可能是区分大小写的。本节描述这种行为，
以及如何在必要时修改它。

假设一个查询在 `SCHEMATA.SCHEMA_NAME` 列搜索名为 `test` 的数据库，在 Linux 上文
件系统是区分大小写的，所以 `SCHEMATA.SCHEMA_NAME` 和 'test' 匹配, 但不匹配
'TEST':
```
mysql> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA
WHERE SCHEMA_NAME = 'test';
+-------------+
| SCHEMA_NAME |
+-------------+
| test |
+-------------+

mysql> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA
WHERE SCHEMA_NAME = 'TEST';
Empty set (0.00 sec)
```
如果系统变量 `lower_case_table_names` 设置为 0，则出现上面的结果。如果设置为 1
或 2，则第二个查询返回的结果将和第一个查询一样。

    注意：
    禁止启动服务器时使用和服务器初始化时不同的 `lower_case_table_names` 设置。

在 Windows 或 macOS，文件系统不区分大小写，所以 'test' 和 'TEST' 都匹配:
```
mysql> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA
WHERE SCHEMA_NAME = 'test';
+-------------+
| SCHEMA_NAME |
+-------------+
| test        |
+-------------+

mysql> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA
WHERE SCHEMA_NAME = 'TEST';
+-------------+
| SCHEMA_NAME |
+-------------+
| TEST        |
+-------------+
```
`lower_case_table_names` 的值在这种上下文中不起作用。

前面的行为之所以发生，是因为在搜索与文件系统中表示的对象相对应的值时，校对规则
`utf8_general_ci` 并不用于 `INFORMATION_SCHEMA` 查询。

如果在 `INFORMATION_SCHEMA` 列上的字符串操作的结果与期望不同，一个变通的解决方案
就是使用显式的 `COLLATE` 子句来强制使用适当的校对规则（参见 Section 10.8.1, “
Using COLLATE in SQL Statements”）。例如，为了执行不区分大小写的搜索，使用
`COLLATE` 与 `INFORMATION_SCHEMA` 列名:
```
mysql> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA
WHERE SCHEMA_NAME COLLATE utf8_general_ci = 'test';
+-------------+
| SCHEMA_NAME |
+-------------+
| test        |
+-------------+

mysql> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA
WHERE SCHEMA_NAME COLLATE utf8_general_ci = 'TEST';
+-------------+
| SCHEMA_NAME |
+-------------+
| test |
+-------------+
```

还可以使用 UPPER() 或 LOWER() 函数：
```
WHERE UPPER(SCHEMA_NAME) = 'TEST'
WHERE LOWER(SCHEMA_NAME) = 'test'
```

尽管在具有区分大小写的文件系统的平台上可以执行不区分大小写的比较，但正如刚才所显
示的那样，它并不一定总是正确的事情。在这样的平台上，可能有多个名称，只是字母大小
写不同。例如，命名为 `city`、`CITY` 和 `City` 的表可以同时存在。考虑一个搜索是否
应该匹配所有这样的名称，或者只匹配一个，并相应地编写查询。下面的第一个比较（使用
`utf8_bin`）是区分大小写的;其他的不是:
```
WHERE TABLE_NAME COLLATE utf8_bin = 'City'
WHERE TABLE_NAME COLLATE utf8_general_ci = 'city'
WHERE UPPER(TABLE_NAME) = 'CITY'
WHERE LOWER(TABLE_NAME) = 'city'
```

在 `INFORMATION_SCHEMA` 字符串列中搜索 `INFORMATION_SCHEMA` 本身的值确实使用了
`utf8_general_ci` 校对规则，因为 `INFORMATION_SCHEMA` 是一个“虚拟”数据库，没有
在文件系统中表示。例如，无论哪个平台，`SCHEMATA.SCHEMA_NAME` 都匹配
'information_schema' 或 'INFORMATION_SCHEMA'：
```
mysql> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA
WHERE SCHEMA_NAME = 'information_schema';
+--------------------+
| SCHEMA_NAME |
+--------------------+
| information_schema |
+--------------------+

mysql> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA
WHERE SCHEMA_NAME = 'INFORMATION_SCHEMA';
+--------------------+
| SCHEMA_NAME        |
+--------------------+
| information_schema |
+--------------------+
```
