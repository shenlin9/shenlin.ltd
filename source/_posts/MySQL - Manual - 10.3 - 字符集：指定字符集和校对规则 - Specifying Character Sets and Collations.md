---
title: MySQL - Manual - 10.3 - 字符集：指定字符集和校对规则 - Specifying Character Sets and Collations
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 字符集：MySQL 字符集和校对规则

<!--more-->

在四个层级上有字符集和校对规则的默认设置：服务器、数据库、表和列。下面几节中的描
述可能看起来很复杂，但在实践中发现，多级默认会导致自然和明显的结果。

`CHARACTER SET` 用于在子句中指定字符集，`CHARSET` 可以作为 `CHARACTER SET` 的同
义词使用。

字符集问题不仅影响数据存储，还影响客户端程序和 MySQL 服务器之间的通信。如果您希
望客户端程序使用与缺省字符集不同的值来与服务器通信，那么您需要指出是哪一个字符集
。例如，要使用 `utf8mb4` Unicode 字符集，在连接到服务器后执行此语句：
```
SET NAMES 'utf8mb4';
```

有关在客户端/服务器通信中与字符集设置相关问题的更多信息，参考：Section 10.4,“
Connection Character Sets and Collations”.

## 10.3.1 Collation Naming Conventions 

MySQL 校对规则名称遵循以下约定：

* 校对规则名以与之关联的字符集的名称开始，通常后跟一个或多个后缀，指示其他校对规
  则特征。例如，校对规则 `utf8mb4_general_ci` 和 `latin1_swedish_ci` 分别对应字
  符集 `utf8mb4` 和 `latin1`。 `binary` 字符集有一个单独的校对规则，命名也是
  `binary`，没有后缀。

* 特定于某种语言的校对规则包括一个地区代码或语言名称。例如，
  `utf8mb4_tr_0900_ai_ci` 和 `utf8mb4_hu_0900_ai_ci` 校对规则都是对 `utf8mb4` 字
  符集排序，但分别使用土耳其语和匈牙利语的规则。`utf8mb4_turkish_ci` 和
  `utf8mb4_hungarian_ci` 类似，但基于最近版本的 Unicode 校对规则算法。

* 校对规则的后缀表示校对规则是否大小写敏感，是否口音/重音敏感，是否是二进制的。
  下表显示了用来表示这些特征的后缀。

  ```
  Suffix    Meaning
  _ai       Accent insensitive，口音不敏感
  _as       Accent sensitive，口音敏感
  _ci       Case insensitive，大小写不敏感
  _cs       case-sensitive，大小写敏感
  _ks       Kana sensitive，假名敏感（日语字母，由汉字简化而来）
  _bin      Binary，二进制
  ```

  对于没有指定重音/口音敏感性的非二进制校对规则名称，由大小写敏感性决定口音敏感
  性：如果一个校对规则名不包含 `_ai` 或 `_as` ，那么名字中 `_ci` 就隐含着 `_ai`
  ，而名字中 `_cs` 隐含着 `_as` 。例如， `latin1_general_ci` 是显式的大小写不敏
  感和隐式地口音不敏感， `latin1_general_cs` 是显式的大小写敏感和隐式的重音敏感
  ，而`utf8mb4_0900_ai_ci` 是显式大小写不敏感和重音不敏感的。

  对于日本的校对规则， `_ks` 的后缀表示校对规则是假名敏感的;也就是说，它将片假名
  字符与平假名字符区分开来。没有 `_ks` 后缀的日本校对规则则不是假名敏感的，在排
  序时将片假名和平假名字符同等对待。

  对于二进制字符集的二进制校对规则，比较是基于数字字节值的。对于非二进制字符集的
  `_bin` 校对规则，比较是基于字符编码数字值，这与多字节字符的字节值不同。要了解
  更多信息，请参阅 Section 10.8.5,“The binary Collation Compared to `_bin`
  Collations”

* 对于 Unicode 字符集，校对规则的名字可以包括一个版本号，用来表明 Unicode 校对规
  则算法（Unicode Collation Algorithm，UCA）的版本，基于 UCA 的校对规则如果名字
  里没有版本号，则使用 UCA 4.0.0 版本，例如：
    * `utf8mb4_0900_ai_ci` is based on UCA 9.0.0 weight keys
      (http://www.unicode.org/Public/UCA/9.0.0/allkeys.txt).
    * `utf8mb4_unicode_520_ci` is based on UCA 5.2.0 weight keys
      (http://www.unicode.org/Public/UCA/5.2.0/allkeys.txt).
    * `utf8mb4_unicode_ci` (with no version named) is based on UCA 4.0.0 weight
      keys (http:// www.unicode.org/Public/UCA/4.0.0/allkeys-4.0.0.txt).
    * weight keys？？？

* 对于 Unicode 字符集，`xxx_general_mysql500_ci` 校对规则保留了 5.1.24 之前原始
  的 `xxx_general_ci` 的校对规则的顺序，并且允许 MySQL 5.1.24 之前的创建的表升级
  (Bug #27877)。

## 10.3.2 Server Character Set and Collation 

MySQL 服务器有服务器级的字符集和校对规则，可以在服务器启动时通过命令行或在选项文
件里设置，还可以在运行时更改。

首先，服务器字符集和校对规则取决于你启动 `mysqld` 服务器时使用的选项，可以使用
`--character-set-server` 来设置字符集，通过 `--collation-server` 设置校对规则。
如果你没有指定字符集，就相当于设置 `--character-set-server=utf8mb4`。如果你只指
定了字符集（例如 `utf8mb4`）但没指定校对规则，就相当于设置
`--character-set-server=utf8mb4 --collation-server=utf8mb4_0900_ai_ci`，因为
`utf8mb4_0900_ai_ci` 是 `utf8mb4` 的默认校对规则。所以，下列三个命令效果相同：
```
mysqld
mysqld --character-set-server=utf8mb4
mysqld --character-set-server=utf8mb4 --collation-server=utf8mb4_0900_ai_ci
```

改变设置的一种方法是重新编译。要在从源代码构建时更改默认服务器字符集和校对规则，
请使用 `CMake` 的 `DEFAULT_CHARSET` 和 `DEFAULT_COLLATION` 选项。例如:
```
cmake . -DDEFAULT_CHARSET=latin1
```
或者:
```
cmake . -DDEFAULT_CHARSET=latin1 -DDEFAULT_COLLATION=latin1_german1_ci
```

`mysqld` 和 `CMake` 都会验证字符集和校对规则组合是否有效。如果没有，则每个程序都
会显示一条错误消息并终止。

如果没有在 `CREATE DATABASE` 语句中指定数据库字符集和校对规则，那么就用服务器字
符集和校对规则作默认值。他们没有别的目的。

服务器字符集和校对规则的当前值可以通过系统变量 `character_set_server` 和
`collation_server` 的值确定。这些变量可以在运行时更改。

## 10.3.3 Database Character Set and Collati

每个数据库都有一个数据库级别的字符集和校对规则，`CREATE DATABASE` 和 `ALTER
DATABASE` 有可选的子句用来指定它们：
```
CREATE DATABASE db_name
[[DEFAULT] CHARACTER SET charset_name]
[[DEFAULT] COLLATE collation_name]

ALTER DATABASE db_name
[[DEFAULT] CHARACTER SET charset_name]
[[DEFAULT] COLLATE collation_name]
```
可以用关键字 `SCHEMA` 代替 `DATABASE`

`CHARACTER SET` 和 `COLLATE` 子句使得可以在同一个 MySQL 服务器上使用不同的字符集
和校对规则来创建数据库。

数据库的选项保存在数据字典里，并且可以通过 `INFORMATION_SCHEMA.SCHEMATA` 表查看

例如：
```
CREATE DATABASE db_name CHARACTER SET latin1 COLLATE latin1_swedish_ci;
```

MySQL 使用下面的方式选择数据库的字符集和校对规则：

* 如果同时指定了 `CHARACTER SET charset_name` 和 `COLLATE collation_name`，则使
  用指定的字符集 `charset_name` 和校对规则 `collation_name`

* 如果指定了 `CHARACTER SET charset_name` 但没有指定 `COLLATE`，则使用指定的字符
  集 `charset_name` 和它默认的校对规则。查看每个字符集的默认校对规则，使用 `SHOW
  CHARACTER SET` 语句。

* 如果指定了 `COLLATE collation_name` 但没有指定 `CHARACTER SET`，则使用指定的校
  对规则 `collation_name` 以及和 `collation_name` 关联的字符集。

* 否则，既没有指定 `CHARACTER SET` 也没有指定 `COLLATE`，则使用服务器的字符集和
  校对规则。 

默认数据库的字符集和校对规则可以通过系统变量 `character_set_database` 和
`collation_database` 的值来确定。每当默认数据库发生变化时，服务器都会设置这些变
量。如果没有默认数据库，则变量具有与相应的服务器级系统变量相同的值，即
`character_set_server` and `collation_server`。

要查看一个给定数据库的默认字符集和校对规则，使用下列语句：
```
USE db_name;
SELECT @@character_set_database, @@collation_database;
```

或者，不改变默认数据库而显示值的方法：
```
SELECT DEFAULT_CHARACTER_SET_NAME, DEFAULT_COLLATION_NAME
FROM INFORMATION_SCHEMA.SCHEMATA WHERE SCHEMA_NAME = 'db_name';
```

数据库字符集和校对规则会影响服务器操作的这些方面：

* 对于 `CREATE TABLE` 语句，如果没有指定表的字符集和校对规则的话，那么数据库字符
  集和校对就用作表定义的默认值。要覆盖这个，提供显式的 `CHARACTER SET` 和
  `COLLATE` 表选项

* 对于没有包含 `CHARACTER SET` 子句的 `LOAD DATA` 语句，服务器使用系统变量
  `character_set_database` 表明的字符集来解释文件里的信息。可以通过显式的
  `CHARACTER SET` 子句覆盖掉。

* 对于存储例程（过程和函数），如果字符数据参数的声明没有包括 `CHARACTER SET` 和
  `COLLATE` 属性，数据库字符集和校对规则在例程创建时就被用作字符数据参数的字符集
  和校对规则。要覆盖这个，可以通过提供显式的 `CHARACTER SET` 和 `COLLATE` 属性。

## 10.3.4 Table Character Set and Collation

每个表都有一个表级的字符集和校对规则，`CREATE TABLE` 和 `ALTER TABLE` 语句都有可
选的子句用来指定表的字符集和校对规则。
```
CREATE TABLE tbl_name (column_list)
[[DEFAULT] CHARACTER SET charset_name]
[COLLATE collation_name]]

ALTER TABLE tbl_name
[[DEFAULT] CHARACTER SET charset_name]
[COLLATE collation_name]
```

例如：
```
CREATE TABLE t1 ( ... )
CHARACTER SET latin1 COLLATE latin1_danish_ci;
```

MySQL 使用下面的方式来选择字符集和校对规则：

* 如果同时指定了 `CHARACTER SET charset_name` 和 `COLLATE collation_name`，则使
  用指定的字符集 `charset_name` 和校对规则 `collation_name`

* 如果指定了 `CHARACTER SET charset_name` 但没有指定 `COLLATE`，则使用指定的字符
  集 `charset_name` 和它默认的校对规则。查看每个字符集的默认校对规则，使用 `SHOW
  CHARACTER SET` 语句。

* 如果指定了 `COLLATE collation_name` 但没有指定 `CHARACTER SET`，则使用指定的校
  对规则 `collation_name` 以及和 `collation_name` 关联的字符集。

* 否则，既没有指定 `CHARACTER SET` 也没有指定 `COLLATE`，则使用数据库的字符集和
  校对规则。 

如果列字符集和校对规则没有在单独的列定义中指定的话，那么表字符集和校对就用作列定
义的默认值。表级的字符集和校对规则是 MySQL 的扩展;在标准 SQL 中没有这样的东西。

## 10.3.5 Column Character Set and Collation

每个“字符”列（就是说，列类型为 CHAR, VARCHAR, TEXT）都有一个列级的字符集和校对
规则，`CREATE TABLE` 和 `ALTER TABLE` 里的列的定义语法有可选的子句用来指定列的字
符集和校对规则：
```
col_name {CHAR | VARCHAR | TEXT} (col_length)
[CHARACTER SET charset_name]
[COLLATE collation_name]
```

这些子句也可以用于 `ENUM` 和 `SET` 列：
```
col_name {ENUM | SET} (val_list)
[CHARACTER SET charset_name]
[COLLATE collation_name]
```

例如：
```
CREATE TABLE t1
(
    col1 VARCHAR(5)
    CHARACTER SET latin1
    COLLATE latin1_german1_ci
);

ALTER TABLE t1 MODIFY
col1 VARCHAR(5)
CHARACTER SET latin1
COLLATE latin1_swedish_ci;
```

MySQL 使用下列方式来确定列的字符集和校对规则：

* 如果同时指定了 `CHARACTER SET charset_name` 和 `COLLATE collation_name`，则使
  用指定的字符集 `charset_name` 和校对规则 `collation_name`

    ```
    CREATE TABLE t1
    (
    col1 CHAR(10) CHARACTER SET utf8 COLLATE utf8_unicode_ci
    ) CHARACTER SET latin1 COLLATE latin1_bin;
    ```

  为列指定了字符集和校对规则，则使用为列指定的。即列的字符集为 `utf8` 校对规则为
  `utf8_unicode_ci`

* 如果指定了 `CHARACTER SET charset_name` 但没有指定 `COLLATE`，则使用指定的字符
  集 `charset_name` 和它默认的校对规则。查看每个字符集的默认校对规则，使用 `SHOW
  CHARACTER SET` 语句。

    ```
    CREATE TABLE t1
    (
    col1 CHAR(10) CHARACTER SET utf8
    ) CHARACTER SET latin1 COLLATE latin1_bin;
    ```

  为列指定了字符集，但没指定校对规则。则列的字符集是指定的 `utf8`，校对规则是
  `utf8` 的默认校对 `utf8_general_ci`。

* 如果指定了 `COLLATE collation_name` 但没有指定 `CHARACTER SET`，则使用指定的校
  对规则 `collation_name` 以及和 `collation_name` 关联的字符集。

    ```
    CREATE TABLE t1
    (
    col1 CHAR(10) COLLATE utf8_polish_ci
    ) CHARACTER SET latin1 COLLATE latin1_bin;
    ```

  为列指定了校对规则，但没有指定字符集。则列使用指定的校对规则 `utf8_polish_ci`
  ，而字符集是与校对规则相关联的，也就是 `utf8`。

* 否则，既没有指定 `CHARACTER SET` 也没有指定 `COLLATE`，则使用表的字符集和校对
  规则。 

    ```
    CREATE TABLE t1
    (
    col1 CHAR(10)
    ) CHARACTER SET latin1 COLLATE latin1_bin;
    ```

  没有为列指定任何字符集和校对规则，则使用表的默认值。即字符集 `latin1` 和校对规
  则 `latin1_bin`。

`CHARACTER SET` 和 `COLLATE` 子句都是标准的 SQL 语句。

如果你使用 `ALTER TABLE` 把一个列从一个字符集转换到另一个字符集，则 MySQL 会尝试
进行数据值的映射，但如果字符集不兼容，则可能有数据丢失。

## 10.3.6 Character String Literal Character Set and Collation

每一个字符串文字量都有一个字符集和校对规则。

对于简单的语句 `SELECT 'string'`，字符串具有由系统变量
`character_set_connection` 和 `collation_connection` 定义的连接默认字符集和校对
规则。

字符串文字量可以有一个可选的字符集引入器和 `COLLATE` 子句，将其指定为一个使用特
定字符集和校对规则的字符串：
```
[_charset_name]'string' [COLLATE collation_name]
```
字符集引入器和 `COLLATE` 子句是根据标准 `SQL` 规范实现的。

例如：
```
SELECT 'abc';
SELECT _latin1'abc';
SELECT _binary'abc';
SELECT _utf8'abc' COLLATE utf8_danish_ci;
```

`_charset_name` 表达式正式名称为引入器。它告诉解析器：“后面跟着的字符串使用的字
符集是 `charset_name`”。引入器不会使得字符串使用引入器的字符集，而
`CONVERT()` 则会这样。引入器不改变字符串值，虽然可能会填充。引入器只是一个信号。
（即引入器告诉解释器，后面的字符串要按什么字符集来处理，如果后面的字符串不是使用
的引入器所说明的字符集，则应该会出错）

MySQL 通过下面的方式来确定字符串文字量的字符集和校对规则：

* 如果同时指定了 `_charset_name` 和 `COLLATE collation_name`，则使用指定的字符集
  `charset_name` 和校对规则 `collation_name`，`collation_name` 必须是
  `charset_name` 允许的校对规则。

* 如果指定了 `_charset_name` 但没有指定 `COLLATE`，则使用指定的字符
  集 `charset_name` 和它默认的校对规则。查看每个字符集的默认校对规则，使用 `SHOW
  CHARACTER SET` 语句。

* 如果指定了 `COLLATE collation_name` 但没有指定 `_charset_name`，则使用系统变量
  `character_set_connection` 指定的连接默认字符集和指定的校对规则
  `collation_name`。`collation_name` 必须是连接默认字符集允许的校对规则。

* 否则，既没有指定 `_charset_name` 也没有指定 `COLLATE collation_name`，则使用系
  统变量 `character_set_connection` 和 `collation_connection ` 指定的连接默认字
  符集和校对规则

例如：

* 一个非二进制字符串使用 `latin1` 字符集和 `latin1_german1_ci` 校对规则
    ```
    SELECT _latin1'Müller' COLLATE latin1_german1_ci;
    ```
* 一个非二进制字符串使用 `utf8` 字符集和它默认的校对规则 (也就是 `utf8_general_ci`):
    ```
    SELECT _utf8'Müller';
    ```
* 一个二进制字符串使用 `binary` 字符集和它默认的校对规则 (也就是 `binary`):
    ```
    SELECT _binary'Müller';
    ```
* 一个非二进制字符串使用连接默认字符集和 `utf8_general_ci` 校对规则 (如果连接字
  符集不是 `utf8` 则失败):
    ```
    SELECT 'Müller' COLLATE utf8_general_ci;
    ```
* 一个字符串使用连接默认字符集和校对规则:
    ```
    SELECT 'Müller';
    ```

引入器表明了跟在后面的字符串使用的字符集，但不会改变解析器在字符串中执行转义处理
的方式。解析器解释转义总是根据 `character_set_connection` 所给出的字符集。

下面的例子展示了即使使用了引入器，依然使用 `character_set_connection` 指定的字符
集进行转义处理。例子中使用的 `SET NAMES` 改变了 `character_set_connection` （参
考：Section 10.4, “Connection Character Sets and Collations”）的值，然后使用
`HEX()` 函数显示结果字符串，使得可以看见准确的字符串内容。

例子 1:
```
mysql> SET NAMES latin1;
mysql> SELECT HEX('à\n'), HEX(_sjis'à\n');
+------------+-----------------+
| HEX('à\n')   | HEX(_sjis'à\n') |
+------------+-----------------+
| E00A          | E00A             |
+------------+-----------------+
```
上例中, `à` (十六进制值 `E0`) 后跟着 `\n`, 新行的转义序列. 转义序列被解释时使用
的是 `character_set_connection` 的值 `latin1` 生成了一个文字换行符(十六进制值
`0A`)。第二个字符串同样被照此处理，也就是说，引入器 `_sjis` 没有影响到解析器的转
义处理。

例子 2:
```
mysql> SET NAMES sjis;
mysql> SELECT HEX('à\n'), HEX(_latin1'à\n');
+------------+-------------------+
| HEX('à\n') | HEX(_latin1'à\n') |
+------------+-------------------+
| E05C6E      | E05C6E |
+------------+-------------------+
```

上例中，`character_set_connection` 是 `sjis`，在这个字符集里 “à”和后面的 “\
”组成的序列是一个有效的多字节字符（虽然它们是两个字符，十六进制值分别是 05 和
5C） ，因此，字符串的前两个字节被解释为了一个 `sjis` 字符，“\”不再被解释为转义
字符。后面的 `n`（十六进制 `6E`）也不再解释为转义序列的一部分。对于第二个字符串
也是这样处理，即引入器 `_latin1` 没有影响到解析器的转义处理。

## 10.3.7 The National Character Set

标准 SQL 定义了 `NCHAR` 或 `NATIONAL CHAR`，这些列其实是使用了一些预定义字符集的
`CHAR` 列。MySQL 使用 `utf8` 作为这些列的预定义字符集。例如，这些数据类型声明是
等价的：
```
CHAR(10) CHARACTER SET utf8
NATIONAL CHARACTER(10)
NCHAR(10)
```
这些也一样：
```
VARCHAR(10) CHARACTER SET utf8
NATIONAL VARCHAR(10)
NVARCHAR(10)

NCHAR VARCHAR(10)
NATIONAL CHARACTER VARYING(10)
NATIONAL CHAR VARYING(10)
```

可以使用 `N'literal'` 或 `n'literal'` 的形式来创建一个使用国家字符集的字符串，下
列语句等价：
```
SELECT N'some text';
SELECT n'some text';
SELECT _utf8'some text';
```

## 10.3.8 Character Set Introducers

字符串字面量、十六进制字面量、位值字面量都可以有一个可选的字符集引入器和
`COLLATE` 子句，用来将其指定为一个使用特定字符集和校对规则的字符串：
```
[_charset_name] literal [COLLATE collation_name]
```
字符集引入器和 `COLLATE` 子句是根据标准SQL规范实现的。

例如：
```
SELECT 'abc';
SELECT _latin1'abc';
SELECT _binary'abc';
SELECT _utf8'abc' COLLATE utf8_danish_ci;
SELECT _latin1 X'4D7953514C';
SELECT _utf8 0x4D7953514C COLLATE utf8_danish_ci;
SELECT _latin1 b'1000001';
SELECT _utf8 0b1000001 COLLATE utf8_danish_ci;
```

`_charset_name` 表达式正式名称为引入器。它告诉解析器：“后面跟着的字符串使用的字
符集是 `charset_name`”。引入器不会使得紧跟的字符串使用引入器指定的字符集，而
`CONVERT()` 则会这样。引入器不改变字符串值，虽然可能会填充。引入器只是一个信号。

对于字符串字面值，引入器和字符串之间允许有空格也可以没有空格。

通过使用 `_binary` 引入器，可以把字符串字面值指定为二进制字符串。十六进制字面值
和位字面值默认是二进制字符串，可以使用 `_binary` 引入器，但通常不需要。在十六进
制字面值或位值字面值被当作数字对待的上下文中，`_binary` 引入器可以用来保留它们作
为二进制字符串。例如，在 MySQL 8.0 和更高版本里，位操作允许使用数字或二进制字符
串作为参数，但默认是把十六进制字面量和位字面量作为数字处理。要显式的为这些字面量
指定二进制字符串上下文，请至少为一个参数使用 `_binary` 引入器：
```
mysql> SET @v1 = X'000D' | X'0BC0';
mysql> SET @v2 = _binary X'000D' | X'0BC0';
mysql> SELECT HEX(@v1), HEX(@v2);
+----------+----------+
| HEX(@v1) | HEX(@v2) |
+----------+----------+
| BCD | 0BCD |
+----------+----------+
```
对于两个位操作，显示的结果看起来相似，但没有 `_binary` 的结果是一个 `BIGINT` 值
，而有 `_binary` 的结果是一个二进制字符串。有欲结果类型的不同，显示的值也不同：
数值结果没有显示高阶位的 0。

MySQL 使用下面的方式确实字符串字面量、十六进制字面量、位字面量的字符集和校对规则
：

* 如果同时指定了 `_charset_name` 和 `COLLATE collation_name`，则使用指定的字符集
  `charset_name` 和校对规则 `collation_name`，`collation_name` 必须是
  `charset_name` 允许的校对规则。

* 如果指定了 `_charset_name` 但没有指定 `COLLATE`，则使用指定的字符
  集 `charset_name` 和它默认的校对规则。查看每个字符集的默认校对规则，使用 `SHOW
  CHARACTER SET` 语句。

* 如果指定了 `COLLATE collation_name` 但没有指定 `_charset_name`：

    * 对于字符串字面值，则使用系统变量 `character_set_connection` 指定的连接默认
      字符集和指定的校对规则 `collation_name`。`collation_name` 必须是连接默认字
      符集允许的校对规则。

    * 对于十六进制字面值或位字面值，唯一允许的校对规则是 `binary`，因为这些类型
      的字面值默认是二进制字符串。

* 否则，既没有指定 `_charset_name` 也没有指定 `COLLATE collation_name`：

    * 对于字符串字面值，则使用系统变量 `character_set_connection` 和
      `collation_connection ` 指定的连接默认字符集和校对规则

    * 对于十六进制字面值或位字面值，字符集和校对规则是 `binary`

举例:

* 非二进制字符串使用 `latin1` 字符集和 `latin1_german1_ci` 校对规则:
    ```
    SELECT _latin1'Müller' COLLATE latin1_german1_ci;
    SELECT _latin1 X'0A0D' COLLATE latin1_german1_ci;
    SELECT _latin1 b'0110' COLLATE latin1_german1_ci;
    ```

* 非二进制字符串使用 `utf8` 字符集和默认的校对规则（也就是 `utf8_general_ci`）:
    ```
    SELECT _utf8'Müller';
    SELECT _utf8 X'0A0D';
    SELECT _utf8 b'0110';
    ```
* 二进制字符串使用 `binary` 字符集和它默认的校对规则（也就是 `binary`）：
    ```
    SELECT _binary'Müller';
    SELECT X'0A0D';
    SELECT b'0110';
    ```

  十六进制字面值和位字面值不需要引入器，因为它们默认就是二进制字符串。

* 一个非二进制字符串使用连接默认字符集和 `utf8_general_ci` 校对规则 (如果连接字
  符集不是 utf8 则失败):
    ```
    SELECT 'Müller' COLLATE utf8_general_ci;
    ```
  上述例子（只有 `COLLATE`）不适用于十六进制字面量或位字面量，因为不管连接字符集
  是什么，它们的字符集是 `binary`，是不兼容于 `utf8_general_ci` 校对规则的。没有
  引入器时它们唯一允许的 `COLLATE` 子句是 `COLLATE binary`。

* 一个字符串使用连接默认字符集和校对规则；
    ```
    SELECT 'Müller';
    ```

对于字符集字面量，引入器指出了后面字符串的字符集，但不会改变解析器在字符串中执行
转义处理的方式。解析器总是根据 `character_set_connection` 提供的字符集来解释转义
。有关额外的讨论和示例，请参见 Section 10.3.6, “Character String Literal
Character Set and Collation”。

## 10.3.9 例子s of Character Set and Collation Assignment 

下面的例子展示了 MySQL 如何确定默认的字符集和校对规则：

例子 1: 表和列定义
```
CREATE TABLE t1
(
    c1 CHAR(10) CHARACTER SET latin1 COLLATE latin1_german1_ci
) DEFAULT CHARACTER SET latin2 COLLATE latin2_bin;
```
这里的列使用 `latin1` 字符集和 `latin1_german1_ci` 校对规则，定义很明确，所以很
简单。注意在字符集为 `latin2` 的表里保存字符集为 `latin1` 的列是没问题的。

例子 2: 表和列定义
```
CREATE TABLE t1
(
    c1 CHAR(10) CHARACTER SET latin1
) DEFAULT CHARACTER SET latin1 COLLATE latin1_danish_ci;
```

这里的列使用 `latin1` 字符集和它默认的校对规则。尽管列的校对规则从表级获取看起来
很自然，但是列默认的校对规则并不是从表级获取的。因为 `latin1` 的默认校对规则总是
`latin1_swedish_ci`，所以列 `c1` 的校对规则是 `latin1_swedish_ci`，而不是表的
`latin1_danish_ci`。

例子 3: 表和列定义
```
CREATE TABLE t1
(
    c1 CHAR(10)
) DEFAULT CHARACTER SET latin1 COLLATE latin1_danish_ci;
```

一个带有默认字符集和默认校对规则的列。在这种情况下，MySQL 检查表级以确定列的字符
集和校对规则。因此，列 `c1` 的字符集是 `latin1`，它的校对规则是
`latin1_danish_ci`。

例子 4: 数据库、表和列定义
```
CREATE DATABASE d1
DEFAULT CHARACTER SET latin2 COLLATE latin2_czech_ci;

USE d1;
CREATE TABLE t1
(
    c1 CHAR(10)
);
```
我们创建了一个列，但没有指定它的字符集和校对规则。我们也没有在表级指定字符集和校
对规则。在这种情况下，MySQL 通过检查数据库级的设置来确定表级的设置，然后成为列级
的设置。）因此，列c1的字符集是 `latin2`，它的校对规则是 `latin2_czech_ci`。

## 10.3.10 Compatibility with Other DBMSs

对于 MaxDB 兼容性，下面这两个语句是相同的
```
CREATE TABLE t1 (f1 CHAR(N) UNICODE);
CREATE TABLE t1 (f1 CHAR(N) CHARACTER SET ucs2);
```
