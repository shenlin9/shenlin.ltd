---
title: MySQL - Manual - 9.1 - 语言结构：字面值 - Literal Values
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 语言结构：字面值

<!--more-->

本节描述如何在 MySQL 中编写字面值。这些包括字符串、数字、十六进制和位值、布尔值
和 NULL。本节还介绍了在 MySQL 中处理这些基本类型时可能遇到的各种细微差别。

## 9.1.1 String Literals

字符串是一个字节或字符序列，包含在单引号（'）或双引号（"）字符中。例子:
```
'a string'
"another string"
```

相邻的几个引号引起来的字符串会被连接到一个单独的字符串中。以下几行是等价的：
```
'a string'
'a' ' ' 'string'
```

如果启用了 `ANSI_QUOTES` SQL 模式，字符串字面值只能在单引号内引用，因为在双引号
中引用的字符串被解释为标识符。

二进制字符串是一连串字节。每个二进制字符串都有一个名为 `binary` 的字符集和校对规
则。非二进制字符串是一连串字符。它有一个除 `binary` 之外的字符集，以及与字符集兼
容的校对规则。

对于这两种字符串，比较都是基于字符串单元的数值:
* 对于二进制字符串，单元是字节，比较使用的是字节的数字值。
* 对于非二进制字符串，单元是字符，且一些字符集支持多字节字符，比较使用的是字符代
  码的数字值。
字符编码顺序是字符串校对规则的功能。（欲了解更多信息，请参阅 Section 10.8.5, “
The binary Collation Compared to `_bin` Collations”）。

字面意义的非二进制字符串可以有一个可选的字符集引入器(introducer)和 `COLLATE` 子
句，从而将它指定为一个使用特定字符集和校对规则的字符串：
```
[_charset_name]'string' [COLLATE collation_name]
```

例如:
```
SELECT _latin1'string';
SELECT _binary'string';
SELECT _utf8'string' COLLATE utf8_danish_ci;
```

你可以通过这个格式：`N'literal'` 或者 `n'literal'` 来使用国家字符集(national
character set)创建字符串. 下列语句都是等价的：
```
SELECT N'some text';
SELECT n'some text';
SELECT _utf8'some text';
```

有关这些字符串语法形式的信息参考 Section 10.3.7, “The National Character Set”,
and Section 10.3.8, “Character Set Introducers”.

在一个字符串序列中，某些序列具有特殊的含义，除非启用了 `NO_BACKSLASH_ESCAPES`
SQL 模式。这些有特殊含义的序列都以一个反斜杠（\）开头，即转义字符。MySQL 识别下
表中的转义序列：

**特殊字符转义序列**

    转义序列    序列所代表的字符
    \0          一个ASCII NUL字符 (X'00') 
    \'          一个单引号
    \"	        一个双引号
    \b	        一个退格键
    \n	        一个换行符
    \r	        一个回车符
    \t	        一个制表符
    \\	        一个反斜线
    \Z	        ASCII 26 (Control+Z); 看下面详细说明
    \%	        一个百分号字符; 看下面详细说明
    \_	        一个下划线字符; 看下面详细说明

    详细说明：

    ASCII 26 字符可以被转义编码为 `\Z`，使您能够处理 Windows 系统中 ASCII 26 代
    表的 `END-OF-FILE` 的问题。如果您试图使用 `mysql db_name < file_name`，文件
    中的 ASCII 26 会导致问题。

    `\%` 和 `\_` 序列用于在模式匹配上下文中搜索 `%` 和 `_` 字面意义的实例，否则
    它们将被解释为通配符。请参阅此章节中的 `LIKE` 操作符的描述：Section 12.5.1,
    “String Comparison Functions”。

    如果你在模式匹配上下文之外使用 `\%` 或 `\_` ，则被视为对字符串 `\%` 和 `\_`
    评估，而不是 `%` 和 `_` 进行评估。

对于所有其他转义序列，反斜杠被忽略。也就是说，这个转义字符被解释为没有转义。例如
，`\x` 就是 `x`。这些转义序列是区分大小写的。例如，`\b` 被解释为一个退格，但是
`\B` 被解释为 `B`。转义处理是根据系统变量 `character_set_connection` 所指示的字
符集完成的。即使是字符串前面的表明了不同字符集的引入器也是这样，具体参考：
Section 10.3.6,“Character String Literal Character Set and Collation”.

有几种方法可以在字符串中包含引号字符：
* A ' inside a string quoted with ' may be written as ''.
* A " inside a string quoted with " may be written as "".
* Precede the quote character by an escape character (\).
* A ' inside a string quoted with " needs no special treatment and need not be doubled or escaped. In the same way, " inside a string quoted with ' needs no special treatment.

下面的 SELECT 语句演示了引用和转义如何工作：
```
mysql> SELECT 'hello', '"hello"', '""hello""', 'hel''lo', '\'hello';
+-------+---------+-----------+--------+--------+
| hello | "hello" | ""hello"" | hel'lo | 'hello |
+-------+---------+-----------+--------+--------+

mysql> SELECT "hello", "'hello'", "''hello''", "hel""lo", "\"hello";
+-------+---------+-----------+--------+--------+
| hello | 'hello' | ''hello'' | hel"lo | "hello |
+-------+---------+-----------+--------+--------+

mysql> SELECT 'This\nIs\nFour\nLines';
+--------------------+
| This
Is
Four
Lines |
+--------------------+

mysql> SELECT 'disappearing\ backslash';
+------------------------+
| disappearing backslash |
+------------------------+
```

为了将二进制数据插入到字符串列中（例如 BLOB 列），您应该通过转义序列来表示某些字
符。反斜杠（\）和用来引用字符串的引号必须被转义。在某些客户端环境中，可能还需要
转义 `NUL` 或 `Control+Z`。mysql 客户端会在没有转义的情况下将包含 `NUL` 字符的字
符串截掉，而在 Windows 中如果没有转义 `NUL` 则可以使用 `Control+Z` 当作是文件结
束(END-OF-FILE)。对于代表每个字符的转义序列，请参见上表。

在编写应用程序程序时，任何可能包含这些特殊字符的字符串都必须正确地转义，才能将字
符串的数据值发送到 MySQL 服务器的 SQL 语句中。你可以用两种方式来做：

* 用一个转义特殊字符的函数来处理字符串：
    * 在 C 程序里, 可以使用 C API 函数  `mysql_real_escape_string_quote()` 来转
      义字符. See Section 27.7.7.56,“mysql_real_escape_string_quote()”.
    * 在构建其他 SQL 语句的 SQL 语句中，你可以使用 `QUOTE()` 函数.
    * Perl DBI 接口提供了一种引用方法，可以将特殊字符转换为适当的转义序列 See
      Section 27.9, “MySQL Perl API”. Other language interfaces may provide a
      similar capability.
* 作为显式转义特殊字符的另一种选择，许多 MySQL APIs 提供了一个占位符功能，使您能
  够将特殊标记插入到语句字符串中，然后在发出语句时将数据值绑定到它们。在这种情况
  下，API 负责为你转义值中的特殊字符。

## 9.1.2 Numeric Literals

数字字面值包括精确值（整数integer和小数DECIMAL）字面值和近似值（浮点
floating-point）字面值。

整数被表示成一个数位序列。
数字可能包括了 `.` 作为一个小数分隔符。
在数字之前，可能会有 `-` 或 `+` 来表示一个负的或正的值。
用尾数和指数的科学计数法表示的数字是接近值数字。

精确值的数字字面值有一个整数部分或小数部分，或者两者都有。他们可能是有符号的。例
子: `1, .2, 3.4, -5, -6.78, +9.10`.

近似值的数字字面值表示为使用尾数和指数的科学计数法，两部分都可以有或没有符号。例
如：`1.2E3, 1.2E-3, -1.2E3, -1.2E-3`.

两个看起来相似的数字可能会被区别对待。例如，`2.34` 是一个精确值（定点）数，而
`2.34E0` 是一个近似值（浮点）数。

DECIMAL(小数)数据类型是一个定点类型，计算是精确的。在 MySQL 中，DECIMAL(小数)有
几个同义词：NUMERIC、DEC、FIXED。
整数类型也是精确值类型。要了解更多关于精确值计算的信息，see Section 12.23, “
Precision Math”.

`FLOAT` 和 `DOUBLE` 数据类型是浮点类型，计算是近似的。在 MySQL 中，`FLOAT` 或
`DOUBLE` 的同义词是 `DOUBLE PRECISION`(双精度) 和 `REAL`(实数)。

一个整数可以在浮点上下文中使用;它被解释为等效的浮点数。

## 9.1.3 Date and Time Literals

日期和时间值可以用几种格式表示，比如数字或引用的字符串，这取决于具体的值类型和其
他因素。例如，在 MySQL 期望日期的上下文中，`'2015-07-21'`、`'20150721'`和
`20150721` 都被解释为日期。

本节描述日期和时间字面值的可接受格式。关于时间数据类型的更多信息，如允许的值范围
，请参考以下部分：
* Section 11.1.2, “Date and Time Type Overview”
* Section 11.3, “Date and Time Types”

**Standard SQL and ODBC Date and Time Literals.**

标准 SQL 允许使用类型关键字和字符串指定时间字面值。关键字和字符串之间的空格是可
选的：
```
DATE 'str'
TIME 'str'
TIMESTAMP 'str'
```

MySQL 识别这些结构和相应的 ODBC 语法：
```
{ d 'str' }
{ t 'str' }
{ ts 'str' }
```
MySQL 使用类型关键字和上述结构分别生成 `DATE`、`TIME` 和 `DATETIME` 值，如果指定
了还包括末尾的小数秒(fractional seconds)部分，例如 `2014-09-08 17:51:04.78`。

`TIMESTAMP` 语法在 MySQL 中产生 `DATETIME` 值，因为 `DATETIME` 的范围更接近于标
准 SQL 的 `TIMESTAMP` 类型，标准 SQL 的 `TIMESTAMP` 的年代范围从 `0001` 到
`9999` 。（MySQL 的 `TIMESTAMP` 年代范围是 1970 年到 2038 年)

**String and Numeric Literals in Date and Time Context.**

MySQL 认识下列格式的 `DATE` 值：

* 形如 `'YYYY-MM-DD'` 或 `'YY-MM-DD'` 格式的字符串。还允许使用“宽松”的语法：任何标
  点符号都可以用作日期部件之间的分隔符。例如，“2012-12-31”、“2012/12/31”、“
  2012^12^31^”和“2012@12@31”是等同的。

* 形如 `'YYYYMMDD'` 或 `'YYMMDD'` 格式，没有分隔符的字符串，前提是该字符串作为日
  期是有意义的。例如，“20070523”和“070523”被解释为“2007-05-23”，但“071332
  ”是非法的（月、日无意义），所以就变成了“00000000”。

* 形如 `YYYYMMDD` 或 `YYMMDD` 格式的数字, 前提是数字作为日期是有意义的，例如
  19830905 和 830905 被解释为 '1983-09-05'.

MySQL 认识下列格式的 `DATETIME` 和 `TIMESTAMP` 值：

* 形如 `'YYYY-MM-DD HH:MM:SS'` 或 `'YY-MM-DD HH:MM:SS'` 格式的字符串。还允许使用
  “宽松”的语法：任何标点符号都可以用作日期或时间部件之间的分隔符。例如，
  '2012-12-31 11:30:45', '2012^12^31 11+30+45', '2012/12/31 11*30*45',
  '2012@12@31 11^30^45' 是等同的。

  日期时间部分和小数秒部分之间唯一识别的分隔符是小数点。

  日期和时间部分除了空格也可以由字符 `T` 分隔，例如 '2012-12-31 11:30:45'
  '2012-12-31T11:30:45' 是等价的。

* 形如 `'YYYYMMDDHHMMSS'` 或 `'YYMMDDHHMMSS'` 格式，没有分隔符的字符串。前提是字
  符串作为日期是有意义的，例如：'20070523091528' 和 '070523091528' 被解释为
  '2007-05-23 09:15:28', 但是 '071122129015' 不合法(分钟部分无意义) 所以变为
  '0000-00-00 00:00:00'.

* 形如 `YYYYMMDDHHMMSS` 或 `YYMMDDHHMMSS` 格式的数字，前提是数字作为日期有意义，
  例如：19830905132800 和 830905132800 被解释为 '1983-09-05 13:28:00'.

一个 `DATETIME` 或 `TIMESTAMP` 的值可以包括尾部的微秒（6位数）精度的小数秒部分。
小数秒部分应该总是和其他的时间部分用小数点隔开;没有其他小数秒分隔符可以被识别。

关于 MySQL 里对小数秒的支持，参考：Section 11.3.6, “Fractional Seconds in Time
Values”.

Dates containing two-digit year values are ambiguous because the century is unknown. MySQL interprets two-digit year values using these rules:
包含两位数年值的日期是模糊的，因为世纪是未知的。MySQL 使用下列规则解释两位数的年
份值：
* 70-99的年值被转换为1970-1999年。
* 00-69的年值被转换为2000-2069年。
参考：Section 11.3.8, “Two-Digit Years in Dates”.

指定为包含日期分隔符的字符串，没有必要为小于 10 的月或日指定两个数字。“2015-6-9
”与“2015-06-09”相同。类似地，包括时间分隔符的字符串，则没有必要为小于10的小时
、分钟或秒值指定两个数字。“2015-10-30 1:2:3”与“2015-10-30 01:02:03”相同。

指定为非分隔符字符串的值根据它们的长度来解释：
* 对于 8 个或 14 个字符的字符串，会假设年份是由前四个字符给出的。其他长度则假定
  年份为前两个字符。
* 字符串从左到右解释查找年份、月、日、小时、分钟和秒，这意味着不应该使用少于6个
  字符的字符串。例如，如果你指定了“9903”，认为它代表了1999年3月，MySQL 将它转
  换为“零”日期值。这是因为年份和月份的值是99和03，但是日的部分完全丢失了。但是
  ，您可以显式地指定 0 值来表示丢失的月份或日部分。例如，要插入“1999-03-00”使
  用“990300”。

指定为数字的值应该是6、8、12或14位长：
* 如果一个数字是8或14位长，那么它被假定为 `YYYYMMDD` 或 `YYYYMMDDHHMMSS` 格式，
  而年是由前4位数字给出的。
* 如果数字是6或12位长，那么它被假定为 `YYMMDD` 或 `YYMMDDHHMMSS` 格式，而年是由
  前两个数字给出的。
* 不是这些长度的数字被解释为用前导零填充到最接近的长度。

MySQL 认识下列格式的 `TIME` 值：

* 形如 `'D HH:MM:SS'` 格式的字符串，可以使用下列的宽松语法：`'HH:MM:SS'`,
  `'HH:MM'`, `'D HH:MM'`, `'D HH'` 或 `'SS'`. 这里 D 代表天数，可以从 0 到 34。

* 形如 `'HHMMSS'` 格式且没有分隔符的字符串，前提是作为时间有意义。例如：
  `'101112'` 被解释为 `'10:11:12'` 但是 `'109712'` 非法(分钟无意义) 所以被解释为
  `'00:00:00'`.

* 形如 `HHMMSS` 格式的数字，前提是作为时间有意义。例如：101112 被解释为
  '10:11:12'. 下面的替代格式也可以被理解: SS, MMSS, HHMMSS.

后面的小数秒部分被识别为：`'D HH:MM:SS.fraction'`, `'HH:MM:SS.fraction'`,
`'HHMMSS.fraction'`, `HHMMSS.fraction` 时间格式，小数秒达到微秒（6位数）精度。小
数部分应该总是和其他的时间部分用小数点隔开;没有其他小数秒分隔符可以被识别。关于
MySQL 中小数秒支持的信息，参见:Section 11.3.6, “Fractional Seconds in Time
Values”.

对于指定为字符串的时间值，包括时间部分分隔符，没有必要为小于10的小时、分钟或秒值
指定两个数字。'8:3:2' 和 '08:03:02' 是一样的。

## 9.1.4 Hexadecimal Literals

Hexadecimal literal values are written using X'val' or 0xval notation, where val contains hexadecimal digits (0..9, A..F). Lettercase of the digits and of any leading X does not matter. A leading 0x is case-sensitive and cannot be written as 0X.

十六进制的字面值：
* 用 `X'val'` 或 `0xval` 格式编写，`val` 包含十六进制数字（0...9,A..F)，数字和
  任何前导 `X` 的字母大小写都不重要。
* 前置的 `0x` 是大小写敏感的，不能写成 `0X`。

合法的十六进制字面值：
```
X'01AF'
X'01af'
x'01AF'
x'01af'
0x01AF
0x01af
```

非法的十六进制字面值：
```
X'0G'       (G 不是十六进制数字)
0X01AF      (0X 必须小写为 0x)
```

使用 `X'val'` 符号编写的值必须包含偶数个数字否则出现语法错误。为了修正这个问题，
可以用一个前导零来填充：
```
mysql> SET @s = X'FFF';
ERROR 1064 (42000): You have an error in your SQL syntax;
check the manual that corresponds to your MySQL server
version for the right syntax to use near 'X'FFF''

mysql> SET @s = X'0FFF';
Query OK, 0 rows affected (0.00 sec)
```

但使用包含奇数位数字的 `0xval` 符号编写的值被视为具有额外的前导 0。例如，`0xaaa`
被解释为 `0x0aaa`。

在默认情况下，十六进制字面值是一个二进制字符串，其中每一对十六进制数字代表一个字
符：
```
mysql> SELECT X'4D7953514C', CHARSET(X'4D7953514C');
+---------------+------------------------+
| X'4D7953514C' | CHARSET(X'4D7953514C') |
+---------------+------------------------+
| MySQL         | binary                 |
+---------------+------------------------+

mysql> SELECT 0x5461626c65, CHARSET(0x5461626c65);
+--------------+-----------------------+
| 0x5461626c65 | CHARSET(0x5461626c65) |
+--------------+-----------------------+
| Table        | binary                |
+--------------+-----------------------+
```

十六进制字面值可以有一个可选的字符集引入器和 `COLLATE` 子句，用于将它指定为一个
使用特定字符集和校对规则的字符串：
```
[_charset_name] X'val' [COLLATE collation_name]
```

例如：
```
SELECT _latin1 X'4D7953514C';
SELECT _utf8 0x4D7953514C COLLATE utf8_danish_ci;
```
例子使用了 `X'val'` 符号，但是 `0xval` 符号也允许引入器。有关引入器的信息，请参
阅 Section 10.3.8, “Character Set Introducers”.

在数字上下文中，MySQL 将十六进制字面值作为 `BIGINT`（64位整数）对待。为了确保十
六进制字面值的数字处理，在数字上下文中使用它，方法包括将其加 0 或使用
`CAST(... AS UNSIGNED)`。例如，在默认情况下，分配给用户自定义变量的十六进制字面
值是二进制字符串。要将值赋值为一个数字，在数值上下文中使用它：
```
mysql> SET @v1 = X'41';
mysql> SET @v2 = X'41'+0;
mysql> SET @v3 = CAST(X'41' AS UNSIGNED);
mysql> SELECT @v1, @v2, @v3;
+------+------+------+
| @v1   | @v2   | @v3|
+------+------+------+
| A     | 65    | 65 |
+------+------+------+
```

空的十六进制值 (`X''`) 为 0 长度的二进制字符串，转换为数字则为 0：
```
mysql> SELECT CHARSET(X''), LENGTH(X'');
+--------------+-------------+
| CHARSET(X'') | LENGTH(X'') |
+--------------+-------------+
| binary       | 0           |
+--------------+-------------+

mysql> SELECT X''+0;
+-------+
| X''+0 |
+-------+
| 0 |
+-------+
```

`X'val'` 符号是基于标准 SQL 的，`0x` 符号是基于 ODBC, 后者的十六进制字符串常被用
于 `BLOB` 列的值。

要将字符串或数字转换成十六进制格式的字符串，请使用 `HEX()` 函数：
```
mysql> SELECT HEX('cat');
+------------+
| HEX('cat') |
+------------+
| 636174     |
+------------+

mysql> SELECT X'636174';
+-----------+
| X'636174' |
+-----------+
| cat       |
+-----------+
```

对于十六进制字面值来说，位运算被认为是数字上下文，但是位操作允许在 MySQL 8.0 和
更高版本中使用数字或二进制字符串参数。为了显式地为十六进制字面值指定二进制字符串
上下文，为至少一个参数使用 `_binary` 引入器：
```
mysql> SET @v1 = X'000D' | X'0BC0';
mysql> SET @v2 = _binary X'000D' | X'0BC0';
mysql> SELECT HEX(@v1), HEX(@v2);
+----------+----------+
| HEX(@v1)  | HEX(@v2) |
+----------+----------+
| BCD       | 0BCD     |
+----------+----------+
```

显示的结果在两个位操作中都是相似的，但是没有 `_binary` 的结果是一个 `BIGINT` 值
，而 `_binary` 的结果是一个二进制字符串。由于结果类型的不同，显示的值不同：数值
结果没有显示高位数字 0。

## 9.1.5 Bit-Value Literals

位值的字面值书写格式：`b'val'` 或 `0bval`。
`val` 是 0 和 1 的二进制值。
`b'val'` 里的前导 b 大小写不重要；
但 `0bval` 里的 `b` 必须是小写的，不能大写为 `0B`。

合法的位值的字面值
```
b'01'
B'01'
0b01
```

不合法的位值的字面值
```
b'2' (2 不是二进制数字)
0B01 (0B 必须写为 0b)
```

默认情况下，位值的字面值是一个二进制字符串
```
mysql> SELECT b'1000001', CHARSET(b'1000001');
+------------+---------------------+
| b'1000001' | CHARSET(b'1000001') |
+------------+---------------------+
| A | binary |
+------------+---------------------+

mysql> SELECT 0b1100001, CHARSET(0b1100001);
+-----------+--------------------+
| 0b1100001 | CHARSET(0b1100001) |
+-----------+--------------------+
| a | binary |
+-----------+--------------------+
```

两种格式的位值字面值都可以有一个可选的字符集引入器和 `COLLATE` 子句，从而将其指
定为一个使用特定字符集和校对规则的字符串：
```
[_charset_name] b'val' [COLLATE collation_name]
```

例如：
```
SELECT _latin1 b'1000001';
SELECT _utf8 0b1000001 COLLATE utf8_danish_ci;
```
关于引入器的信息，参考 Section 10.3.8, “Character Set Introducers”.

在数字上下文中，MySQL 将位值字面值视为整数对待。为了确保对位值字面值的数字处理，
即在数字上下文中使用它，方法包括加 0 或使用 `CAST(... AS UNSIGNED)`。例如，在默
认情况下，分配给用户自定义变量的位值字面值是二进制字符串。要将值赋值为一个数字，
在数值上下文中使用它：
```
mysql> SET @v1 = b'1100001';
mysql> SET @v2 = b'1100001'+0;
mysql> SET @v3 = CAST(b'1100001' AS UNSIGNED);
mysql> SELECT @v1, @v2, @v3;
+------+------+------+
| @v1   | @v2   | @v3|
+------+------+------+
| a     | 97    | 97 |
+------+------+------+
```

空的位值 `b''` 是 0 长度的二进制字符串，转换为数字是 0
```
mysql> SELECT CHARSET(b''), LENGTH(b'');
+--------------+-------------+
| CHARSET(b'') | LENGTH(b'') |
+--------------+-------------+
| binary | 0 |
+--------------+-------------+

mysql> SELECT b''+0;
+-------+
| b''+0 |
+-------+
| 0 |
+-------+
```

位值的表示法可以很方便地为 `BIT` 列分配值：
```
mysql> CREATE TABLE t (b BIT(8));
mysql> INSERT INTO t SET b = b'11111111';
mysql> INSERT INTO t SET b = b'1010';
mysql> INSERT INTO t SET b = b'0101';
```

结果集中的位值作为二进制值返回，这可能不能很好地显示。要将位值转换为可打印的形式
，请在数值上下文中使用它或者使用诸如 `BIN()` 或 `HEX()` 之类的转换函数。在转换
后的值中没有显示高位的数字 0。
```
mysql> SELECT b+0, BIN(b), OCT(b), HEX(b) FROM t;
+------+----------+--------+--------+
| b+0   | BIN(b)    | OCT(b) | HEX(b) |
+------+----------+--------+--------+
| 255   | 11111111  | 377    | FF   |
| 10    | 1010      | 12     | A    |
| 5     | 101       | 5      | 5    |
+------+----------+--------+--------+
```

对于位字面量(bit literals)来说，位操作被认为是数值上下文，但是在 MySQL 8.0 和更
高版本中位操作允许使用数字或二进制字符串参数，所以要显式地为位字面量指定二进制字
符串上下文，请为至少其中一个参数使用 `_binary` 引入器：
```
mysql> SET @v1 = b'000010101' | b'000101010';
mysql> SET @v2 = _binary b'000010101' | _binary b'000101010';
mysql> SELECT HEX(@v1), HEX(@v2);
+----------+----------+
| HEX(@v1)  | HEX(@v2) |
+----------+----------+
| 3F        | 003F      |
+----------+----------+
```

两个位操作的显示结果都是相似的，但是没有使用 `_binary` 的结果是一个 `BIGINT` 值，
而使用了 `_binary` 的结果是一个二进制字符串。由于结果类型的不同，显示的值不同：
数值结果没有显示高位数字 0。

## 9.1.6 Boolean Literals

常量 `TRUE` 和 `FALSE` 分别计算为 1 和 0。常量名可以用任何大小写字母来写。
```
mysql> SELECT TRUE, true, FALSE, false;
-> 1, 1, 0, 0
```

## 9.1.7 NULL Values

NULL 值表示“没有数据”，NULL 可以用任何大小写字母来写。

注意 “NULL”值不同于数值类型的 0 或字符串类型的空字符串等值。更多信息参考：
Section B.5.4.3, “Problems with NULL Values”.

使用 `LOAD DATA INFILE` 或 `SELECT ... INTO OUTFILE` 对文本文件执行导入或导出操
作时，`NULL` 由 `\N` 序列表示。参考：Section 13.2.7, “LOAD DATA INFILE Syntax”.

使用 `ORDER BY` 排序时， 升序排序时 `NULL` 值排在其他值前面，降序排序时排在其他
值后面。
