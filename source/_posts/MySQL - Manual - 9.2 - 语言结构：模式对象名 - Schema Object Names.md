---
title: MySQL - Manual - 9.2 - 语言结构：模式对象名 - Schema Object Names
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 语言结构：模式对象名

<!--more-->

MySQL 中的某些对象，包括数据库、表、索引、列、别名、视图、存储过程、分区、表空间
、资源组和其他对象名称都称为标识符。本节描述 MySQL 标识符的允许语法。Section
9.2.2, “Identifier Case Sensitivity”描述了哪些类型的标识符在什么条件下是区分大
小写的。

标识符可以被引号引起来，也可以不引起来。如果标识符包含特殊字符或是保留字，则必须
在引用时引起来。（例外：在限定名称内后面跟着点号(period)的保留字必须是一个标识符
，所以它不需要被引起来）。保留字列表：Section 9.3, “Keywords and Reserved Words
”.

标识符在内部转换为 Unicode。它们可能包含这些字符：
* 未引起来的标识符中允许的字符：
    * ASCII: `[0-9,a-z,A-Z$_]` (基本的拉丁字母，数字0-9，美元，下划线)
    * Extended: U+0080 .. U+FFFF
* 引起来的标识符中的允许字符包括完整的 Unicode 基本多语言平面（BMP），除了 U+0000：
    * ASCII: U+0001 .. U+007F
    * Extended: U+0080 .. U+FFFF
* 在引用或未引用的标识符中不允许 ASCII NUL（U+0000）和补充字符（U+10000和更高）。
* 标识符可以从一个数字开始，但是除非是引用标识符不能仅仅由数字组成。
* 数据库、表和列名不能以空格字符结束。

标识符引用字符是反引号(````):
```
mysql> SELECT * FROM `select` WHERE `select`.id > 100;
```

如果启用了 `ANSI_QUOTES` SQL 模式, 标识符也可以使用双引号：
```
mysql> CREATE TABLE "test" (col INT);
ERROR 1064: You have an error in your SQL syntax...

mysql> SET sql_mode='ANSI_QUOTES';
mysql> CREATE TABLE "test" (col INT);
Query OK, 0 rows affected (0.00 sec)
```

`ANSI_QUOTES` 模式使得服务器将双引号引起来的字符串解释为标识符。因此，当启用此模
式时，字符串字面量必须包含在单引号内。它们不能用双引号括起来。控制服务器的 SQL
模式，参考：Section 5.1.10, “Server SQL Modes”.

如果您引用标识符的话，标识符引用字符可以包含在标识符中。如果在标识符中包含的字符
与用来引用标识符本身的字符相同，那么您需要将字符加倍写两次。下面的语句创建了一个
名为 `a``b` 的表，其中包含一个名为 `c"d` 的列：
```
mysql> CREATE TABLE `a``b` (`c"d` INT);
```

在查询的 select 列表中，可以使用标识符或字符串引用字符来指定引用的列别名：
```
mysql> SELECT 1 AS `one`, 2 AS 'two';
+-----+-----+
| one | two |
+-----+-----+
| 1   | 2   |
+-----+-----+
```
Elsewhere in the statement, quoted references to the alias must use identifier
quoting or the reference is treated as a string literal.
在语句的其他地方，引用引用的引用必须使用标识符引用，或者引用被视为字符串文字。

It is recommended that you do not use names that begin with `Me` or `MeN`, where
`M` and `N` are integers. For example, avoid using `1e` as an identifier,
because an expression such as `1e+3` is ambiguous. Depending on context, it
might be interpreted as the expression `1e + 3` or as the number `1e+3`.
建议您不要使用以 `Me` 或 `MeN` 开头的名称，即 `M` 和 `N` 是整数。例如，避免使用
`1e` 作为标识符，因为像 `1e+3` 这样的表达式是不明确的。根据上下文的不同，它可能
被解释为表达式 `1e + 3` 或科学计数法的数字 `1e+3`。

在使用 `MD5()` 生成表名时要小心，因为它可以以非法或模糊的格式生成名称，比如刚才
描述的那些格式。

用户变量不能直接在 SQL 语句中作为标识符或标识符的一部分使用。更多的信息和解决方
案的例子，参考：Section 9.4, “User-Defined Variables”

把数据库名和表名中的特殊字符编码在对应的文件系统名称中，具体参考：Section 9.2.3,
“Mapping of Identifiers to File Names”.

下表描述了每种类型标识符的最大长度。

    Identifier Type         Maximum Length (characters)
    Database                    64
    Table                       64
    Column                      64
    Index                       64
    Constraint                  64
    Stored Program              64
    View                        64
    Tablespace                  64
    Server                      64
    Log File Group              64
    Alias                       256 (see exception following table)
    Compound Statement Label    16
    User-Defined Variable       64
    Resource Group              64


`CREATE VIEW` 语句中的列名别名是根据 64 个字符的最大列长度进行检查的（而不是 256
个字符的最大别名长度）。

标识符是使用 Unicode（UTF-8）存储的。这适用于表定义中的标识符，以及存储在
`mysql` 数据库中的 `grant` 表中的标识符。`grant` 表中的标识符字符串列的大小是用
字符来度量的。您可以使用多字节字符，而不会减少存储在这些列中的值的字符数量。正如
前面所指出的，允许的 Unicode 字符是位于基本多语言平面（BMP）的。不允许补充字符。

## 9.2.1 Identifier Qualifiers

对象名称可能是非限定或限定。如果上下文对名称的解释是明确的，则允许非限定的名称。
一个限定的名称包含至少一个限定符，通过覆盖默认上下文或提供缺失的上下文来阐明解释
上下文。

例如，下面语句使用非限定名 `t1` 创建了一个表：
```
CREATE TABLE t1 (i INT);
```
因为 `t1` 不包含指定数据库的限定符，所以语句在默认数据库中创建此表。如果没有默认
数据库，就会出现错误。

下面语句使用限定名 `db1.t1` 创建了一个表：
```
CREATE TABLE db1.t1 (i INT);
```
因为 `db1.t1` 包含一个数据库限定符 `db1`，所以该语句在名为 `db1` 的数据库中创建
表 `t1`，而不管默认数据库是哪个。如果没有缺省数据库，则必须指定限定符。如果存在
默认数据库，也可以指定限定符，以便指定与默认数据库不同的数据库，或者如果默认值与
指定的数据库相同，则使数据库更明确。

Qualifiers have these characteristics:
* An unqualified name consists of a single identifier. A qualified name consists of multiple identifiers.
* The components of a multiple-part name must be separated by period (.) characters. The initial parts of a multiple-part name act as qualifiers that affect the context within which to interpret the final identifier.
* The qualifier character is a separate token and need not be contiguous with the associated identifiers.  For example, tbl_name.col_name and tbl_name . col_name are equivalent.
* If any components of a multiple-part name require quoting, quote them individually rather than quoting the name as a whole. For example, write `my-table`.`my-column`, not `my-table.my-column`.
* A reserved word that follows a period in a qualified name must be an identifier, so in that context it need not be quoted.

限定符有这些特点:
* 一个不限定的名称由一个单独的标识符组成。一个限定的名称由多个标识符组成。
* 多部分名称的组件必须由句点（.）字符分隔开。多部分名称的初始部分充当限定符，影响解释最终标识符的上下文。
* 限定符是一个单独的标记，不需要与相关联的标识符保持连续。例如,`tbl_name.col_name` 和 `tbl_name . col_name`是等价的。
* 如果一个多部分名称的任何组件都需要引用，那么就单独引用它们，而不是引用整个名称。例如,应写 `my-table`.`my-column`, 而不是 `my-table.my-column`。
* 一个保留字，前面有个句点（.），在一个限定名内必须是一个标识符，所以在这种上下文里，它不需要被引用。 ？？？

对象名称允许的限定符取决于对象类型：
* 数据库名称是完全限定的，不需要限定符：
    ```
    CREATE DATABASE db1;
    ```
* 一个表、视图或存储程序名可能会被授予一个数据库名限定符。`CREATE` 语句中不限定
  和限定名称的例子：
    ```
    CREATE TABLE mytable ...;
    CREATE VIEW myview ...;
    CREATE PROCEDURE myproc ...;
    CREATE FUNCTION myfunc ...;
    CREATE EVENT myevent ...;

    CREATE TABLE mydb.mytable ...;
    CREATE VIEW mydb.myview ...;
    CREATE PROCEDURE mydb.myproc ...;
    CREATE FUNCTION mydb.myfunc ...;
    CREATE EVENT mydb.myevent ...;
    ```
* 一个触发器是与一个表相关联的，因此任何限定符都是应用到表名的，而不是触发器名：
    ```
    CREATE TRIGGER mytrigger ... ON mytable ...;
    CREATE TRIGGER mytrigger ... ON mydb.mytable ...;
    ```
* 列名可以被赋予多个限定符来表明引用它的语句的上下文，如下表所示：

    Column Reference            Meaning
    col_name                    语句里涉及到的表里的 col_name 列
    tbl_name.col_name           默认数据库里的 tbl_name 表里的 col_name 列
    db_name.tbl_name.col_name   db_name 数据库里的 tbl_name 表里的 col_name 列

  换句话说，一个列名可能被赋予一个表名限定符，表名本身可能被赋予一个数据库名限定
  符。在SELECT语句中不限定和限定的列引用的例子：
    ```
    SELECT c1 FROM mytable
    WHERE c2 > 100;

    SELECT mytable.c1 FROM mytable
    WHERE mytable.c2 > 100;

    SELECT mydb.mytable.c1 FROM mydb.mytable
    WHERE mydb.mytable.c2 > 100;
    ```

您不必在语句中为对象引用指定限定符，除非非限定的引用是模糊的。假设列 c1 只发生在
表 t1，c2 只在 t2 中，而 c 在 t1 和 t2 中。任何对 c 的非限定引用在同时涉及两个表
的语句中都是不明确的，则必须被限定为 `t1.c` 或 `t2.c` 以明确是哪个表：
```
SELECT c1, c2, t1.c FROM t1 INNER JOIN t2
WHERE t2.c > 100;
```

类似地，为了通过同一条 SQL 语句从数据库 db1 中的表 t 和数据库 db2 中的表格 t 中
获取数据，您必须限定表引用：对于这两个表中的列的引用，只需要针对在两个表中都出现
的列名使用限定符。假设此列c1只存在表 db1.t，c2 只存在 db2.t 中，但 c 同时存在
db1.t 和 db2.t。在这种情况下，c是模糊的，必须是限定的，但是c1和c2不需要：
```
SELECT c1, c2, db1.t.c FROM db1.t INNER JOIN db2.t
WHERE db2.t.c > 100;
```

表别名可以更简单地编写限定的列引用：
```
SELECT c1, c2, t1.c FROM db1.t AS t1 INNER JOIN db2.t AS t2
WHERE t2.c > 100;
```

## 9.2.2 Identifier Case Sensitivity

在 MySQL 中，数据库对应于数据目录中的目录。数据库中的每个表对应于数据库目录中的
至少一个文件（可能更多，取决于存储引擎）。触发器也对应于文件。因此，底层操作系统
的大小写敏感性在数据库、表和触发器名称的大小写敏感性中起着重要作用。这意味着这些
名称在 Windows 中不区分大小写，但在大多数 Unix 类型中都是区分大小写的。一个值得
注意的例外是 macOS，它是基于 Unix 的，但是使用了一个不区分大小写的默认文件系统类
型（HFS+）。然而，macOS 也支持 UFS 卷，它对大小写敏感，就像在任何 Unix 上一样。
参见 Section 1.8.1, “MySQL Extensions to Standard SQL”。系统变量
`lower_case_table_names` 也会影响服务器处理标识符大小写敏感性的方式，如本节后面
所述。

    注意：
    尽管数据库名、表名和触发器名在某些平台上大小写不敏感，但是您不应该在同一个语
    句中使用不同的大小写来引用同一个对象。下面的语句不会起作用，因为它引用了一张
    表分别为 my_table 和 MY_TABLE：
    ```
    mysql> SELECT * FROM my_table WHERE MY_TABLE.col=1;
    ```

列、索引、存储例程、事件和资源组名称在任何平台上都是不区分大小写的，列别名也不区分。

然而，日志文件组的名称是区分大小写的。这与标准 SQL 不同。

默认情况下，表别名在 Unix 上是区分大小写的，但在 Windows 或 macOS 上却不区分。下
面的语句在 Unix 上不起作用，因为它引用了别名A和a：
```
mysql> SELECT col_name FROM tbl_name AS a
-> WHERE a.col_name = 1 OR A.col_name = 2;
```
然而，在 Windows 上却允许使用上述的语句。为了避免由这些差异引起的问题，最好采用
一致的约定，例如总是使用小写名称创建和引用数据库和表。建议使用该约定的最大可移植
性和易用性。

表名和数据库名如何存储在磁盘上，并在 MySQL 中使用，受到系统变量
`lower_case_table_names` 的影响。该变量不会影响触发器标识符的大小写敏感性。在
Unix 上，`lower_case_table_names` 的默认值是 0。在 Windows 上，默认值是 1。在
macOS 上，默认值是 2。

`lower_case_table_names` 只能在初始化服务器时进行配置。在服务器初始化完成后改变
`lower_case_table_names` 的值是被禁止的。

`lower_case_table_names` 取值如下所示：

* 0: 表和数据库名称存储在磁盘上，使用 `CREATE Table` 或 `CREATE DATABASE` 语句中
  指定的大小写。名称比较是区分大小写的。如果你在一个对文件名称大小写不敏感（比如
  Windows 或 macOS）的系统上运行 MySQL，你不应该把这个变量设为 0。如果您在不区分
  大小写的文件系统中使用`lower_case_table_names=0`，并使用不同的字母大小写访问
  MyISAM 表名，则可能导致索引损坏。

* 1: 表名以小写形式存储在磁盘上，名称比较不区分大小写。MySQL 在存储和查找中将所
  有表名转换为小写。这种行为也适用于数据库名称和表别名。

* 2: 表和数据库名都存储在磁盘上，使用 `CREATE Table` 或 `CREATE DATABASE` 语句中
  指定的字母大小写，但是 MySQL 在查找时将它们转换为小写。名称比较是不区分大小写
  的。这只适用于不区分大小写的文件系统！InnoDB 表名和视图名是以小写形式存储的，
  因为 `lower_case_table_names=1`。

如果您只在一个平台上使用 MySQL，那么通常不需要使用除缺省值以外的
`lower_case_table_names` 设置。但是，如果您想要在对文件系统大小写敏感性不同的平
台之间传输表，您可能会遇到困难。例如，在 Unix 上，你可以有两个不同的表，名为
`my_table` 和 `MY_TABLE`，但是在 Windows 上，这两个名称被认为是相同的。为了避免
因数据库名或表名的大小写出现的数据传输问题，您有两个选择：

* 在所有系统上使用 `lower_case_table_names=1`。这样做的主要缺点是，当您使用
  `SHOW TABLES` 或 `SHOW DATABASES` 时，您不会看到它们原来名称的字母大小写。

* 在 Unix 上使用 `lower_case_table_names=0` 并且在 Windows 上使用
  `lower_case_table_names=2`。这保留了数据库名和表名的字母大小写。这样做的缺点是
  ，您必须确保您的语句在 Windows 上引用数据库名和表名总是使用正确的字母大小写。
  如果把语句转移到字母大小写很重要的 Unix 上，如果字母大小写不正确，它们就不能工
  作。

  例外：如果您使用的是 InnoDB 表，并且您正在尝试避免这些数据传输问题，那么您应该
  在所有平台上使用 `lower_case_table_names=1`，以迫使名称转换为小写。

如果对象名称的大写形式根据二进制校对规则（a binary collation）是相等的，那么对象
名称可能被认为是重复的。对于游标、条件、过程、函数、保存点、存储例程参数、储存程
序局部变量和插件来说，这是正确的。对于列、约束、数据库、分区、用 `PREPARE` 准备
的语句、表、触发器、用户和用户定义的变量来说，这是不正确的。

文件系统的大小写敏感性可以影响 `INFORMATION_SCHEMA` 表中的字符串列的搜索。
更多信息参考：Section 10.8.7, “Using Collation in INFORMATION_SCHEMA Searches”

## 9.2.3 Mapping of Identifiers to File Names

数据库标识符、表标识符和文件系统中的名称之间有对应关系。基本结构是 MySQL 把每个
数据库表示为数据目录中的一个目录，根据存储引擎的不同，每个表可以在对应的数据库目
录中由一个或多个文件表示。

对于数据和索引文件，磁盘上的精确表示是特定于存储引擎的。这些文件可能存储在数据库
目录中，或者信息可以存储在单独的文件中。`InnoDB` 数据存储在 InnoDB 数据文件中。
如果您通过 InnoDB 来使用表空间，那么您所创建的特定表空间文件将被使用。

任何字符在数据库或表标识符中都是合法的，除了 ASCII NUL（X'00'）。MySQL 在创建数
据库目录或表文件时，对相应的文件系统对象中出现问题的任何字符进行编码：

* 基本的拉丁字母（a..zA..Z），数字（0..9）和下划线（_）被编码为原来的样子。因此
  ，它们的大小写敏感性直接依赖于文件系统特性。

* 所有其他具有大写/小写映射的字母表的国家字母都被编码，如下表所示。代码范围列中
  的值是 `UCS-2` 值。

    ...
    表省略
    ...

  序列中的一个字节编码了字母大小写。例如：`LATIN CAPITAL LETTER A WITH GRAVE` 被
  编码为 `@0G`，而`LATIN SMALL LETTER A WITH GRAVE` 被编码为 `@0g`。在这里，第三
  个字节（`G`或`g`）表示字母大小写。（在不区分大小写的文件系统中，这两个字母都将
  被视为相同的。）

  对于一些块，比如西里尔字母，第二个字节决定了字母大小写。对于其他块，如 Latin1
  补充字符，第三个字节决定了字母大小写。如果序列中的两个字节是字母（在希腊语中是
  扩展的），则最左边的字母字符代表字母大小写。所有其他的字母字节必须是小写的。

* 除了下划线（_）以外的所有非字母字符，以及没有大写/小写映射（如希伯来语）的字母
  表的字母，都使用十六进制表示，使用小写字母表示十六进制数字 `a..f`：
    ```
    0x003F -> @003f
    0xFFFF -> @ffff
    ```
  十六进制值对应于 `ucs2` 双字节字符集中的字符值。

在 Windows 上，当服务器创建相应的文件或目录时，`nul`、`prn` 和 `aux` 等一些名称
是通过追加 `@@@` 来编码的。这发生在所有平台上，是为了在平台之间对应的数据库对象
的可移植性。

## 9.2.4 Function Name Parsing and Resolution

MySQL 支持内置（本机）函数、用户自定义函数（UDFs）和存储函数。

本节描述服务器如何识别内置函数的名称是用作函数调用还是用作标识符，以及不同类型的
函数都使用同一个给定名称时服务器如何确定使用哪个函数。

### Built-In Function Name Parsing

解析器使用默认规则解析内置函数的名称。这些规则可以通过启用 `IGNORE_SPACE` SQL 模
式来改变。

当解析器遇到一个和内置函数同名的单词时，它必须确定名称是表示函数调用还是对标识符
的非表达式引用，如表名或列名。例如，在下列语句中，第一个对 `count` 引用是一个函
数调用，而第二个引用是一个表名：
```
SELECT COUNT(*) FROM mytable;
CREATE TABLE count (i INT);
```

解析器应该可以识别内置函数的名称，只在解析预期为表达式的时候才指示函数调用。也就
是说，在非表达式上下文中，函数名被允许作为标识符。

然而，一些内置函数有特殊的解析或实现方面的考虑，因此解析器在默认情况下使用下列规
则来区分它们的名称是被用作函数调用，还是作为非表达上下文中的标识符：
* 在表达式中使用名称作为函数调用，名称和后面的括号字符 `(` 之间必须没有空格。
* 相反，要使用函数名作为标识符，决不能后面立即跟着括号。

函数调用时要求在函数名和括号之间没有空格的情况只适用于有特殊考虑的内置函数。
`COUNT` 就是这样一个名字。`sql/lex.h` 的源文件列出了这些特殊函数的名称，以下空格
决定了它们对这些特殊函数的解释：`symbols[]` 数组中的 `SYM_FN()` 宏定义的名称。
 The `sql/lex.h` source file lists the names of these special functions for
 which following whitespace determines their interpretation: names defined by
 the `SYM_FN()` macro in the `symbols[]` array.

在 MySQL 8.0 中,有 30 个这样的函数名称。您可能会发现最容易处理的是应用于所有函数
调用的无空格要求。

下面列出了受 `IGNORE_SPACE` 设置影响的函数名和 `sql/lex.h` 源文件里特殊的函数名：

    • BIT_AND
    • BIT_OR
    • BIT_XOR
    • CAST
    • COUNT
    • CURDATE
    • CURTIME
    • DATE_ADD
    • DATE_SUB
    • EXTRACT
    • GROUP_CONCAT
    • MAX
    • MID
    • MIN
    • NOW
    • POSITION
    • STD
    • STDDEV
    • STDDEV_POP
    • STDDEV_SAMP
    • SUBSTR
    • SUBSTRING
    • SUM
    • SYSDATE
    • TRIM
    • VARIANCE
    • VAR_POP
    • VAR_SAMP

对于那些没有在 `sql/lex.h` 中被列为特别的函数，空格并不重要，它们只有用在表达式
上下文时才被解释为函数调用，其他情况则作为标识符自由使用。ASCII 就是一个这样的函
数名。然而，对于这些未受影响的函数名，解释可能在表达式上下文中有所不同：如果有给
定的函数名 `func_name ()`，则被解释为内置函数，如果没有则 `func_name ()` 被解释
为用户自定义函数或存储过程。？？？？

`IGNORE_SPACE` SQL 模式可以用来告诉解析器如何对待对空格敏感的函数名：

* 禁用 `IGNORE_SPACE` SQL 模式后，当名字和后面的括号之间没有空格时，解析器会将名
  字解释为函数调用，即使函数名是用在非表达式上下文中也是这样：
    ```
    mysql> CREATE TABLE count(i INT);
    ERROR 1064 (42000): You have an error in your SQL syntax ...
    near 'count(i INT)'
    ```

  要消除错误并将名称作为标识符处理，要么使用名称后面加空格，要么将其写成引用标识
  符（或两者同时）：
    ```
    CREATE TABLE count (i INT);
    CREATE TABLE `count`(i INT);
    CREATE TABLE `count` (i INT);
    ```

* 在启用 `IGNORE_SPACE` 的情况下，解析器放宽了函数名和后面括号之间必须没有空格的
  要求。这为编写函数调用提供了更大的灵活性。例如，下列函数调用都是合法的：
    ```
    SELECT COUNT(*) FROM mytable;
    SELECT COUNT (*) FROM mytable;
    ```

  然而，启用 `IGNORE_SPACE` 也有一个副作用，即解析器会将受影响的函数名视为保留字
  （参见 Section 9.3, “Keywords and Reserved Words”）。这意味着名称后面的空格
  不能再表明它是作为标识符使用的（即不根据名称后面是否有空格来区分是标识符还是函
  数），即在函数调用中名称后面也可以紧跟或不紧跟空格，但这样在非表达式上下文中时
  会导致语法错误，除非它被引用了。例如，在启用了 `IGNORE_SPACE` 的情况下，下列语
  句都因语法错误失败了，因为解析器将 `count` 解释为一个保留字：
    ```
    CREATE TABLE count(i INT);
    CREATE TABLE count (i INT);
    ```

  要在非表达式上下文中使用函数名，将其写成引用的标识符：
    ```
    CREATE TABLE `count`(i INT);
    CREATE TABLE `count` (i INT);
    ```

要启用 `IGNORE_SPACE` SQL 模式，使用下面语句
```
SET sql_mode = 'IGNORE_SPACE';
```

`IGNORE_SPACE` 也被某些其他的复合模式所启用，比如 ANSI，也在它的值里包括了
`IGNORE_SPACE`：
```
SET sql_mode = 'ANSI';
```
参考 Section 5.1.10, “Server SQL Modes”, 查看哪种复合模式会启用 `IGNORE_SPACE`.

要尽量减少 SQL 代码对 `IGNORE_SPACE` 设置的依赖，可以使用这些指导方针:

* 避免创建与内置函数同名的 UDFs 或存储函数。

* 避免在非表达式上下文中使用函数名。例如，下列语句使用了 `count`（受
  `IGNORE_SPACE` 影响的函数名之一）函数，如果启用 `IGNORE_SPACE`，则无论函数名后
  有没有空格都会失败：
    ```
    CREATE TABLE count(i INT);
    CREATE TABLE count (i INT);
    ```

如果您必须在非表达式上下文中使用函数名，那么将其写成引用的标识符：
```
CREATE TABLE `count`(i INT);
CREATE TABLE `count` (i INT);
```
### Function Name Resolution

下面的规则描述了服务器如何解析对函数名的引用，以便函数创建和调用：

* 内置函数和用户自定义函数
  如果您试图创建一个与内置函数同名的UDF，就会发生错误。

* 内置函数和存储函数
  可以创建与内置函数同名的存储函数，但是为了调用存储的函数，有必要使用模式名来限
  定它。例如，如果你在 `test` 模式中创建了一个名为 `PI` 的存储函数，则应该使用
  `test.PI()` 调用，因为服务器解析没有限定符的 `PI()` 为对内置函数的引用。如果存
  储的函数名与内置函数名相冲突，服务器就会发出警告。警告可以用 `SHOW WARNINGS`
  显示。

* 用户自定义的函数(UDF)和存储函数
  用户自定义的函数和存储函数共享相同的名称空间，因此不能创建具有相同名称的 UDF
  和存储函数。

如果 MySQL 已经升级到了实现了新内置函数的版本，则前面的函数名解析规则对此版本有影响：

* 如果 MySQL 升级后的版本中新内置函数的名字和已存在的用户自定义函数(UDF)名相同，
  那么 UDF 就变得不可访问了。要纠正这个问题，可以使用 `DROP FUNCTION` 删除 UDF
  然后再以不同的非冲突名称用 `CREATE FUNCTION` 重新创建 UDF。然后修改任何受影响
  的代码以使用新名称。

* 如果一个新版本的 MySQL 实现的一个内置函数和现有的一个存储函数具有相同名称,你
  有两个选择:重命名存储函数使用一个不冲突的名字,或改变函数的调用使用一个模式限定符
  (也就是说,使用 `schema_name.func_name()` 语法)。在任何一种情况下，都要相应地修
  改任何受影响的代码。
