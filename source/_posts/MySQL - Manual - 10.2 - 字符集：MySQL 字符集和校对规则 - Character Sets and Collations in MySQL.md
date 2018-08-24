---
title: MySQL - Manual - 10.2 - 字符集：MySQL 字符集和校对规则 - Character Sets and Collations in MySQL
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 字符集：MySQL 字符集和校对规则

<!--more-->

MySQL 服务器支持多个字符集，包括几个 Unicode 字符集。要显示可用的字符集，请使用
`INFORMATION_SCHEMA CHARACTER_SETS` 表或 `SHOW CHARACTER SET` 语句。下面是一个部
分的清单，更完整信息参考：Section 10.10,“Supported Character Sets and Collations”
```
mysql> SHOW CHARACTER SET;
+----------+---------------------------------+---------------------+--------+
| Charset   | Description               | Default collation | Maxlen |
+----------+---------------------------------+---------------------+--------+
| big5      | Big5 Traditional Chinese  | big5_chinese_ci   | 2 |
| binary    | Binary pseudo charset     | binary            | 1 |
...
| latin1    | cp1252 West European      | latin1_swedish_ci | 1 |
...
| ucs2      | UCS-2 Unicode             | ucs2_general_ci   | 2 |
...
| utf8      | UTF-8 Unicode             | utf8_general_ci   | 3 |
| utf8mb4   | UTF-8 Unicode             | utf8mb4_0900_ai_ci | 4 |
...
```

默认情况下，`SHOW CHARACTER SET` 语句显示所有可用的字符集。可以使用可选的 `LIKE`
或 `WHERE` 子句来表示匹配哪个字符集名字。下面的例子展示了一些 Unicode 字符集（都
是基于 Unicode Transformation Format，Unicode 转换格式，UTF）：
```
mysql> SHOW CHARACTER SET LIKE 'utf%';
+---------+------------------+--------------------+--------+
| Charset   | Description       | Default collation | Maxlen |
+---------+------------------+--------------------+--------+
| utf16     | UTF-16 Unicode    | utf16_general_ci  | 4 |
| utf16le   | UTF-16LE Unicode  | utf16le_general_ci| 4 |
| utf32     | UTF-32 Unicode    | utf32_general_ci  | 4 |
| utf8      | UTF-8 Unicode     | utf8_general_ci   | 3 |
| utf8mb4   | UTF-8 Unicode     | utf8mb4_0900_ai_ci| 4 |
+---------+------------------+--------------------+--------+
```

一个给定的字符集总是有至少有一个校对规则，大多数字符集都有几个校对规则。要列出一
个字符集的校对规则，请使用 `INFORMATION_SCHEMA COLLATIONS` 表或 `SHOW COLLATION`
语句。

默认情况下，`SHOW COLLATION` 语句显示所有可用的校对规则。可以使用可选的 `LIKE`
或 `WHERE` 子句来指示要显示哪些校对规则名。例如，要查看默认字符集 `utf8mb4` 的校
对规则，请使用以下语句：
```
mysql> SHOW COLLATION WHERE Charset = 'utf8mb4';
+----------------------------+---------+-----+---------+----------+---------+---------------+
| Collation             | Charset | Id | Default|Compiled | Sortlen | Pad_attribute |
+----------------------------+---------+-----+---------+----------+---------+---------------+
| utf8mb4_0900_ai_ci    | utf8mb4 | 255 | Yes   | Yes       | 0     | NO PAD    |
| utf8mb4_0900_as_ci    | utf8mb4 | 305 |       | Yes       | 0     | NO PAD    |
| utf8mb4_0900_as_cs    | utf8mb4 | 278 |       | Yes       | 0     | NO PAD    |
| utf8mb4_bin           | utf8mb4 | 46  |       | Yes       | 1     | PAD SPACE |
| utf8mb4_croatian_ci   | utf8mb4 | 245 |       | Yes       | 8     | PAD SPACE |
| utf8mb4_cs_0900_ai_ci | utf8mb4 | 266 |       | Yes       | 0     | NO PAD    |
| utf8mb4_cs_0900_as_cs | utf8mb4 | 289 |       | Yes       | 0     | NO PAD    |
| utf8mb4_czech_ci      | utf8mb4 | 234 |       | Yes       | 8     | PAD SPACE |
..................................
.............略...................
..................................
| utf8mb4_unicode_520_ci| utf8mb4 | 246 |       | Yes       | 8     | PAD SPACE |
| utf8mb4_unicode_ci    | utf8mb4 | 224 |       | Yes       | 8     | PAD SPACE |
| utf8mb4_vietnamese_ci | utf8mb4 | 247 |       | Yes       | 8     | PAD SPACE |
| utf8mb4_vi_0900_ai_ci | utf8mb4 | 277 |       | Yes       | 0     | NO PAD    |
| utf8mb4_vi_0900_as_cs | utf8mb4 | 300 |       | Yes       | 0     | NO PAD    |
+----------------------------+---------+-----+---------+----------+---------+---------------+
```
校对规则的更多信息，参考：Section 10.10.1, “Unicode Character Sets”.

校对规则具有以下一般特征：

* 两个不同的字符集不能具有相同的校对规则。

* 每个字符集都有一个默认的校对规则，例如，`utf8mb4` 和 `latin1` 的默认校对规则分
  别是 `utf8mb4_0900_ai_ci` 和 `latin1_swedish_ci`，`INFORMATION_SCHEMA
  CHARACTER_SETS` 表和 `SHOW CHARACTER SET` 语句显式了每个字符集的默认校对规则。
  `INFORMATION_SCHEMA COLLATIONS` 表和 `SHOW COLLATION` 语句有一个列 `Default`
  表明了每个校对规则是否是它字符集的默认规则，如果是则列值为 `YES`，不是则为空。

* 校对规则名称以与它们相关联的字符集的名称开始，通常后面跟着一个或多个后缀，表示
  其他校对规则特征。有关命名约定的附加信息参考 Section 10.3.1, “Collation
  Naming Conventions”.

当一个字符集有多个校对规则时，可能不清楚哪个校对规则最适合给定的应用程序。为了避
免选择不适当的校对规则，可以使用典型的、代表性数据值执行一些比较，以确保给定的校
对规则是以您期望的方式排序数据。

## 10.2.1 Character Set Repertoire

一个字符集的 repertoire 就是此集合里的字符的集合。repertoire，暂时翻译成字库，字
符表，字符库，下面都用字符表表示 repertoire 

字符串表达式有一个字符表属性，取值有两个：
* `ASCII`: 表达式只能包含 Unicode 范围 U+0000 到 U+007F 的字符
* `UNICODE`: 表达式只能包含 Unicode 范围 U+0000 到 U+10FFFF 的字符，即包括基本多
  语平面（Basic Multilingual Plane，BMP，U+0000 to U+FFFF）字符和 BMP 范围外的附
  加字符 (U+10000 to U+10FFFF).

ASCII 范围是 UNICODE 范围的一个子集，因此可以安全地把 ASCII 字符表字符串转换成任
何使用 Unicode 字符表的字符集字符串，或者转换成 ASCII 超集字符集，而不会丢失信息
。（所有 MySQL 字符集都是 ASCII 的超集，除了 swe7 之外，swe7 还为瑞典重音字符重
新使用了一些标点符号），字符表的使用使得可以在表达式里进行字符集转换以解决许多情
况下 MySQL 返回的“illegal mix of collations”（非法混合校对规则）错误。

下面的讨论提供了表达式和字符表的例子，看看字符表的使用怎样改变了字符串表达式的评
估；

* 字符串常量的字符表取决于字符串内容，可能不同于字符串的字符集的字符表，如下列语句：
    ```
    SET NAMES utf8; SELECT 'abc';
    SELECT _utf8'def';
    SELECT N'MySQL';
    ```
  虽然前面每个案例的字符集都是 utf8，但字符串实际上没有包含任何 ASCII 范围外的字
  符，所以它们的字符表是 ASCII 而不是 UNICODE

* 一个有 ASCII 字符集的列有的是 ASCII 字符表，下表中 c1 列有 ASCII 字符表：
    ```
    CREATE TABLE t1 (c1 CHAR(1) CHARACTER SET ascii);
    ```
  下面的例子说明了在没有字符表而导致错误发生的情况下，字符表是如何使得结果变得确
  定的：
    ```
    CREATE TABLE t1 (
    c1 CHAR(1) CHARACTER SET latin1,
    c2 CHAR(1) CHARACTER SET ascii
    );
    INSERT INTO t1 VALUES ('a','b');
    SELECT CONCAT(c1,c2) FROM t1;
    ```
  没有字符表，发生了下列错误:
    ```
    ERROR 1267 (HY000): Illegal mix of collations (latin1_swedish_ci,IMPLICIT)
    and (ascii_general_ci,IMPLICIT) for operation 'concat'
    ```
  使用字符表，子集到超集（ascii到latin1）的转换可以进行，并返回一个结果：
    ```
    +---------------+
    | CONCAT(c1,c2) |
    +---------------+
    | ab            |
    +---------------+
    ```
* 有一个字符串参数的函数继承了参数的字符集，如 `UPPER(_utf8'abc')` 有的是 ASCII
  字符表因为它的参数是 ASCII 字符表。

* 如果函数没有字符串参数，返回的是一个字符串，且使用 `character_set_connection`
  作为字符串结果的字符集，则如果 `character_set_connection` 是 ASCII 则结果的字
  符表就是 ASCII，但 UNICODE 则不是这样：
    ```
    FORMAT(numeric_column, 4);
    ```
  使用字符表会改变 MySQL 对以下示例的评估：
    ```
    SET NAMES ascii;
    CREATE TABLE t1 (a INT, b VARCHAR(10) CHARACTER SET latin1);
    INSERT INTO t1 VALUES (1,'b');
    SELECT CONCAT(FORMAT(a, 4), b) FROM t1;
    ```
  没有字符表，下列错误发生：
    ```
    ERROR 1267 (HY000): Illegal mix of collations (ascii_general_ci,COERCIBLE)
    and (latin1_swedish_ci,IMPLICIT) for operation 'concat'
    ```
  有了字符表，返回结果：
    ```
    +-------------------------+
    | CONCAT(FORMAT(a, 4), b) |
    +-------------------------+
    | 1.0000b |
    +-------------------------+
    ```
* 有两个或两个以上字符串参数的函数，使用“最宽”(UNICODE 比 ASCII 宽)的参数字符
  表作为返回结果的字符表，考虑下面的 `CONCAT()` 调用：
    ```
    CONCAT(_ucs2 X'0041', _ucs2 X'0042')
    CONCAT(_ucs2 X'0041', _ucs2 X'00C2')
    ```
  第一个函数调用，字符表是 ASCII，因为两个参数的范围都在 ascii 字符集之内，第二
  个函数调用，字符表是 UNICODE，因为第二个参数已经超出了 ascii 字符集的范围。

* 函数返回值的字符表是根据影响结果的参数的字符集和校对规则的字符表来确定的。
    ```
    IF(column1 < column2, 'smaller', 'greater')
    ```
  返回值的字符表是 ASCII，因为两个字符串参数（第二个参数和第三个参数）都是 ASCII
  字符表，第一个参数不影响返回值的字符表，即使表达式使用的是字符串值。

## 10.2.2 UTF-8 for Metadata

元数据是“关于数据的数据”。任何描述数据库的东西——而不是数据库的内容——都是元
数据。因此，列名、数据库名、用户名、版本名以及大多数来自“SHOW”的字符串结果都是
元数据。这对于 `INFORMATION_SCHEMA` 中的表的内容也是如此，因为根据定义，这些表包
含关于数据库对象的信息。

元数据的表示必须满足以下要求：
* 所有的元数据必须在相同的字符集中，否则，`INFORMATION_SCHEMA` 表中的 `SHOW` 语
  句和 `SELECT` 语句都不能正常工作，因为这些语句执行结果的同一列的不同行将在不同
  的字符集中。
* 元数据必须包含所有语言中的所有字符。否则，用户将不能使用自己的语言来命名列和表
  。

为了满足这两个要求，MySQL 将元数据存储在 Unicode 字符集中，即 UTF-8。如果您从不
使用重音符号或非拉丁字符，那么这不会造成任何破坏。但是如果您这样做了，您应该意识
到元数据是 UTF-8 格式的。

元数据的两个要求意味着下列函数返回值的默认字符集是 UTF-8：`USER(),
CURRENT_USER(), SESSION_USER(), SYSTEM_USER(), DATABASE(), VERSION()`

服务器把系统变量 `character_set_system` 的值设置为了元数据字符集的名字：
```
mysql> SHOW VARIABLES LIKE 'character_set_system';
+----------------------+-------+
| Variable_name         | Value |
+----------------------+-------+
| character_set_system  | utf8 |
+----------------------+-------+
```

使用 `Unicode` 存储元数据并不意味着服务器在默认情况下返回的列标题和 `DESCRIBE`
函数返回的结果缺省是使用系统变量 `character_set_system` 指定的字符集。当您使用
`SELECT column1 FROM t` 时，列名称 `column1` 本身从服务器返回给客户端使用的字符
集由系统变量 `character_set_results` 的值决定，该变量的缺省值为 `utf8mb4`。如果
您希望服务器将元数据结果返回时用另一个不同的字符集，可以使用 `SET NAMES` 语句来
强制服务器执行字符集转换。`SET NAMES` 设置了 `character_set_results` 和其他相关
的系统变量。（参考：Section 10.4, “Connection Character Sets and Collations”）
，或者，客户端程序可以在收到来自服务器的结果后执行字符集转换。客户端执行转换效
率更高，但这对于所有客户端并不都是可用的。

如果 `character_set_results` 被设置为 `NULL`，则不进行转换，服务器使用它最初的字
符集（`character_set_system` 指定的字符集）返回元数据。 

从服务器返回给客户端的错误消息被自动转换为客户端字符集，和元数据一样。

如果您正在使用（例如）`USER()` 函数在单个语句中进行比较或赋值，请不要担心。MySQL
会为您执行一些自动转换：
```
SELECT * FROM t1 WHERE USER() = latin1_column;
```
这是可行的，因为在比较之前，`latin1_column` 的内容会自动转换为 `UTF-8`。

```
INSERT INTO t1 (latin1_column) SELECT USER();
```
这是可行的，因为在赋值之前，`USER()` 的内容会自动转换为 `latin1`。

尽管自动转换不在 SQL 标准中，但是标准确实说每个字符集都是（在受支持的字符方面）
Unicode 的“子集”。因为这是一个众所周知的原则，即“适用于超集的东西可以应用于一
个子集”，我们相信 Unicode 的校对规则可以应用于非 Unicode 字符串的比较。有关字符
串强制的更多信息，请参见 Section 10.8.4,“Collation Coercibility in Expressions
”
