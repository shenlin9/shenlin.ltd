---
title: MySQL - Manual - 10.9 - 字符集：Unicode 支持 - Unicode Support
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 字符集：Unicode 支持

<!--more-->

Unicode 标准包括基本多语言平面（BMP）的字符和位于 BMP 之外的补充字符。本节描述在
MySQL 中对 Unicode 的支持。有关 Unicode 标准本身的信息，请访问 Unicode 联盟网站。

BMP 字符具有以下特征：
* 它们的代码点值在0和65535之间（或者 U+0000 和 U+FFFF）。
* 它们可以使用8、16或24位（1到3字节）的可变长度进行编码。
* 它们可以使用16位（2字节）的固定长度进行编码。
* 它们对于几乎所有主要语言的字符都是足够的。

补充字符位于 BMP 之外：
* 它们的代码点值在 U+10000 和 U+10FFFF 之间
* Unicode 对补充字符的支持要求字符集字符超出 BMP 范围，因此比 BMP 字符占用更多的
  空间（每个字符多达4个字节）。

UTF-8，Unicode Transformation Format with 8-bit units，使用 8 位单元的 Unicode
转换格式

编码 Unicode 数据的 UTF-8 方法是根据 RFC 3629 实现的，RFC 3629 描述了从一个字节
到四个字节的编码序列，UTF-8 的想法是各种各样的 Uicode 字符使用不同长度的字节序列
进行编码：
* 基本的拉丁字母、数字和标点符号使用一个字节。
* 大多数欧洲和中东的手写字母都适合一个 2 字节的序列：扩展的拉丁字母（带有波浪字
  符、长音符、重音符、沉音符、和其他口音）、西里尔字母、希腊语、亚美尼亚语、希伯
  来语、阿拉伯语、叙利亚语等等。
* 韩国、中国和日本的象形文字使用3字节或4字节的序列。

MySQL 支持下列 Unicode 字符集：
* utf8mb4: Unicode 字符集的 UTF-8 编码，每个字符使用 1 到 4 个字节
* utf8mb3: Unicode 字符集的 UTF-8 编码，每个字符使用 1 到 3 个字节
* utf8: utf8mb3 的别名
* ucs2: Unicode 字符集的 UCS-2 编码，每个字符使用 2 个字节
* utf16: Unicode 字符集的 UTF-16 编码，每个字符使用 2 或 4 个字节，类似 ucs2，但
  有对补充字符的扩展。
* utf16le: Unicode 字符集的 UTF-16LE 编码，像 utf16 但是小端（little-endian）而
  不是大端（big-endian）
* utf32: Unicode 字符集的 UTF-32 编码，每个字符使用 4 个字节。

下表总结了 MySQL 支持的 Unicode 字符集的一般特征

    Character Set       Supported Characters    Required Storage Per Character
    utf8mb3, utf8       BMP only                1, 2, or 3 bytes
    ucs2                BMP only                2 bytes
    utf8mb4             BMP and supplementary   1, 2, 3, or 4 bytes
    utf16               BMP and supplementary   2 or 4 bytes
    utf16le             BMP and supplementary   2 or 4 bytes
    utf32               BMP and supplementary   4 bytes

BMP 之外的字符当转换为只支持 BMP 字符的某些 Unicode 字符集（utf8mb3 或 ucs2）时
，就像替换字符将转换为 '?'。
Characters outside the BMP compare as REPLACEMENT CHARACTER and convert to '?'
when converted to a Unicode character set that supports only BMP characters
(utf8mb3 or ucs2). 

如果您使用支持补充字符的字符集，因此比 BMP-only 的 utf8mb3 和 ucs2 字符集更“宽
”，那么您的应用程序可能存在不兼容的问题。参考 Section 10.9.8, “Converting
Between 3-Byte and 4-Byte Unicode Character Sets”，该部分还描述了如何将表从（3
字节）utf8mb3 转换到（4字节）utf8mb4，以及在此过程中可能应用的约束条件。

大多数 Unicode 字符集都可以使用类似的校对规则。例如，一个丹麦校对规则，名字是
`utf8mb4_danish_ci`, `utf8mb3_danish_ci`, `utf8_danish_ci`, `ucs2_danish_ci`,
`utf16_danish_ci`, 和 `utf32_danish_ci`。唯一的例外是 `utf16le`，它只有两个校对
规则。有关 Unicode 校对规则及其区别属性的信息，包括补充字符的校对规则属性，参见
Section 10.10.1, “Unicode Character Sets”。

UCS-2、UTF-16 和 UTF-32 的 MySQL 实现使用大端字节序存储字符，并且在值开始时不使
用字节顺序标记（BOM，byte order mark）。其他数据库系统可能使用小端字节序或使用
BOM。在这种情况下，当在这些系统和 MySQL 之间传输数据时，需要执行值转换。UTF-16LE
的实现是小端字节序。

MySQL 使用的 UTF-8 值没有 BOM。

使用 Unicode 与服务器通信的客户端应用程序应该相应地设置客户端字符集；例如通过执
行 `SET NAMES 'utf8mb4'` 语句。ucs2、utf16、utf16le 和 utf32 不能用作客户字符集
，这意味着它们不适用于 `SET NAMES` 或 `SET CHARACTER SET`。(See Section 10.4, “
Connection Character Sets and Collations”.)

以下部分提供了 MySQL 中 Unicode 字符集的详细信息。

## 10.9.1 The utf8mb4 Character Set (4-Byte UTF-8 Unicode Encoding)

`utfmb4` 字符集具有以下特征：
* 支持 BMP 和补充字符。
* 每个多字节字符最多需要4个字节。

`utf8mb4` 与 `utf8mb3` 字符集形成了对比，后者只支持 `BMP` 字符，并且每个字符最多
使用 3 个字节：
* 对于 BMP 字符，`utf8mb4` 和 `utf8mb3` 具有相同的存储特性：相同的代码值、相同的
  编码、相同的长度。
* 对于一个补充字符，`utf8mb4` 需要 4 个字节来存储它，而 `utf8mb3` 根本不能存储补
  充字符。当将 `utf8mb3` 列转换为 `utf8mb4` 时，您不必担心补充字符的转换，因为根
  本就没有任何补充字符。

`utf8mb4` 是 `utf8mb3` 的超集，因此对于像下面连接这样的操作，结果的字符集是
`utf8mb4`，校对规则是列 `utf8mb4_col` 的校对规则：
```
SELECT CONCAT(utf8mb3_col, utf8mb4_col);
```

类似的，下面 `WHERE` 子句里的比较也是采用 `utf8mb4_col` 列的校对规则：
```
SELECT * FROM utf8mb3_tbl, utf8mb4_tbl
WHERE utf8mb3_tbl.utf8mb3_col = utf8mb4_tbl.utf8mb4_col;
```

关于数据类型存储的信息，因为它涉及到多字节字符集，参考：Section 11.8 Data Type
Storage Requirements - String Type Storage Requirements.

## 10.9.2 The utf8mb3 Character Set (3-Byte UTF-8 Unicode Encoding)

`utf8mb3` 字符集具有以下特征：
* 只支持 BMP 字符（不支持补充字符）
* 每个多字节字符最多需要 3 个字节。

使用 UTF-8 数据但需要补充字符支持的应用程序应该使用 `utf8mb4` 而不是 `utf8mb3`
(see Section 10.9.1, “The utf8mb4 Character Set (4-Byte UTF-8 Unicode Encoding)
”).

`utf8mb3` 和 `ucs2` 有完全相同的一组字符可供使用。也就是说，他们有相同的字符表（
repertoire）。

`utf8` 是 `utf8mb3` 的别名;字符的限制是隐式的，而不是在名称中显式。

    注意：
    utf8mb3 字符集将在未来的 MySQL 版本中被 utf8mb4 取代。虽然 utf8 目前是
    utf8mb3 的别名，但到那时 utf8 将变为 utf8mb4 的引用。为了避免对 utf8 的含义
    有歧义，可以考虑为字符集引用显式的指定 utf8mb4，而不是 utf8。

`utf8mb3` 可被用于 `CHARACTER SET` 子句，`utf8mb3_collation_substring` 可被用于
`COLLATE` 子句， `collation_substring` 取值：`bin`, `czech_ci`, `danish_ci`,
`esperanto_ci`, `estonian_ci` 等等，例如：

```
CREATE TABLE t (s1 CHAR(1) CHARACTER SET utf8mb3;

SELECT * FROM t WHERE s1 COLLATE utf8mb3_general_ci = 'x';

DECLARE x VARCHAR(5) CHARACTER SET utf8mb3 COLLATE utf8mb3_danish_ci;

SELECT CAST('a' AS CHAR CHARACTER SET utf8) COLLATE utf8_czech_ci;
```

MySQL 立即将 `utf8mb3` 的实例转换为 `utf8`，所以在类似的 `SHOW CREATE TABLE` 或
`SELECT CHARACTER_SET_NAME FROM INFORMATION_SCHEMA.COLUMNS` or `SELECT
COLLATION_NAME FROM INFORMATION_SCHEMA.COLUMNS` 语句里，会看见名字为 `utf8` 或
`utf8_collation_substring`

`utf8mb3` 在除 `CHARACTER SET` 子句之外的上下文中也是有效的，例如：
```
mysqld --character-set-server=utf8mb3

SET NAMES 'utf8mb3'; /* and other SET statements that have similar effect */

SELECT _utf8mb3 'a';
```

关于数据类型存储的信息，因为它涉及到多字节字符集，参考：Section 11.8 Data Type
Storage Requirements - String Type Storage Requirements.

## 10.9.3 The utf8 Character Set (Alias for utf8mb3)

`utf8` 是 `utf8mb3` 字符集的别名，更多信息参考：Section 10.9.2, “The utf8mb3
Character Set (3-Byte UTF-8 Unicode Encoding)”.

    注意：
    utf8mb3 字符集将在未来的 MySQL 版本中被 utf8mb4 取代。虽然 utf8 目前是
    utf8mb3 的别名，但到那时 utf8 将变为 utf8mb4 的引用。为了避免对 utf8 的含义
    有歧义，可以考虑为字符集引用显式的指定 utf8mb4，而不是 utf8。

## 10.9.4 The ucs2 Character Set (UCS-2 Unicode Encoding)

在 UCS-2 中，每个字符都以一个两字节的 Unicode 代码表示，其中最重要的是字节。例如
：“拉丁大写字母A”代码为“0x0041”，它被存储为一个两字节序列：'0x00 0x41'。“西
里尔字母小写字母 YERU”（Unicode'0x044B'）被存储为一个两字节序列：'0x04 0x4B'。
对于 Unicode 字符和它们的代码，请参考 Unicode 联盟网站。

ucs2 字符集具有以下特征：
* 仅支持 BMP 字符（不支持补充字符）
* 使用固定长度的16位编码，每个字符需要两个字节。

## 10.9.5 The utf16 Character Set (UTF-16 Unicode Encoding)

utf16 字符集就是 ucs2 字符集再加上对补充字符编码的扩展：
* 对于 BMP 字符来说，utf16 和 ucs2 具有相同的存储特性：相同的代码值，相同的编码，相同的长度。
* 对于补充字符，utf16 使用一个特殊的序列，用 32 位来表示字符。这被称为“代理”机
  制：对于比 `0xffff` 更大的数字，取 10 位并将它们和 `0xd800` 相加，然后将它们放
  入第一个 16 位字中，再取 10 个位，并将它们和 `0xdc00` 相加，然后将它们放入接下
  来的 16 位字中。因此，所有补充字符都需要 32 位，其中前 16 位是 `0xd800`和
  `0xdbff`之间的数字，最后 16 位是`0xdc00`和`0xdfff`之间的数字。在 Unicode 4.0
  文档的 Section 15.5 Surrogates Area 例子中：

因为 utf16 支持代理，而 ucs2 不支持，所以有一个有效性检查只适用于 utf16：您不能
在没有底部代理的情况下插入一个顶级代理，反之亦然。例如:
```
INSERT INTO t (ucs2_column) VALUES (0xd800); /* legal */
INSERT INTO t (utf16_column)VALUES (0xd800); /* illegal */
```

对于那些在技术上有效但不是真正的 Unicode（也就是说，Unicode 认为是“未分配的代码
点”或“私人使用”字符，甚至是像 `0xffff` 这样的“非法者”）的字符，没有有效性检
查。例如，由于 `U+F8FF` 是苹果的 Logo，这是合法的：
```
INSERT INTO t (utf16_column)VALUES (0xf8ff); /* legal */
```
这样的字符不能指望对每个人都意味着同样的事情。

因为 MySQL 必须允许最坏的情况（一个字符需要4个字节的情况），utf16 列或索引的最大
长度仅为 ucs2 列或索引的最大长度的一半。例如，一个 `MEMORY` 表索引键的最大长度是
3072 字节，因此这些语句创建表时为 ucs2 和 utf16 列创建了允许的最长索引：
```
CREATE TABLE tf (s1 VARCHAR(1536) CHARACTER SET ucs2) ENGINE=MEMORY;
CREATE INDEX i ON tf (s1);

CREATE TABLE tg (s1 VARCHAR(768) CHARACTER SET utf16) ENGINE=MEMORY;
CREATE INDEX i ON tg (s1);
```

## 10.9.6 The utf16le Character Set (UTF-16LE Unicode Encoding)

和 utf16 一样，但是是小端序而不是大端序

## 10.9.7 The utf32 Character Set (UTF-32 Unicode Encoding)

utf32 字符集是固定长度（像 ucs2 不像 utf16）。utf32 为每个字符使用 32 位，不像
ucs2（它为每个字符使用 16 位），并且不像 utf16（对于某些字符使用 16 位而其他字符
使用 32 位）。

utf32 占用的空间是 ucs2 的两倍，比 utf16 的空间更大，但是 utf32 具有与 ucs2 相同
的优势，对于存储它是可以预测的：utf32 所需的字节数等于字符数乘以 4。另外，与
utf16 不同，utf32 中没有用于编码的技巧，因此存储的值等于代码值。

为了演示后一种优势是如何有用的，这里有一个例子，说明了如何根据 utf32 代码的值来
确定 utf8mb4 值：
```
/* Assume code value = 100cc LINEAR B WHEELED CHARIOT */
CREATE TABLE tmp (utf32_col CHAR(1) CHARACTER SET utf32,
utf8mb4_col CHAR(1) CHARACTER SET utf8mb4);

INSERT INTO tmp VALUES (0x000100cc,NULL);

UPDATE tmp SET utf8mb4_col = utf32_col;

SELECT HEX(utf32_col),HEX(utf8mb4_col) FROM tmp;
```

MySQL 对于未分配的 Unicode 字符或私有使用区域字符的添加非常宽容。事实上，utf32
实际上只有一个有效性检查：没有任何代码值可以大于 `0x10ffff`。例如，这是非法的：
```
INSERT INTO t (utf32_column) VALUES (0x110000); /* illegal */
```

## 10.9.8 Converting Between 3-Byte and 4-Byte Unicode Character Sets

本节描述在转换 utf8mb3 和 utf8mb4 字符集之间的字符数据时可能面临的问题。

    注意：
    这个讨论主要集中在 utf8mb3 和 utf8mb4 之间的转换，但是类似的原则适用于在
    ucs2 字符集和 utf16 或 utf32 等字符集之间转换。

utf8mb3 和 utf8mb4 字符集的区别如下：
* utf8mb3 只支持基本的多语言平面（BMP）中的字符。utf8mb4 还额外支持位于 BMP 之外
  的补充字符。
* utf8mb3 每个字符最多使用3个字节。utf8mb4 每个字符最多4个字节。

    注意：
    这里的讨论涉及的 utf8mb3 和 utf8mb4 的字符集名称，是分别显式地引用的 3 字节
    和 4 字节的 UTF-8 字符集数据。例外是在表定义时，MySQL 将这些定义中指定的
    utf8mb3 的实例转换成 utf8，因为 utf8 是 utf8mb3 的别名。

从 utf8mb3 转换到 utf8mb4 的一个优点是，这使得应用程序可以使用补充字符。一个需要
权衡的是这可能会增加数据存储空间的需求。

就表内容而言，从 utf8mb3 到 utf8mb4 的转换没有任何问题：
* 对于 BMP 字符，utf8mb4 和 utf8mb3 具有相同的存储特性：相同的代码值、相同的编码
  、相同的长度。
* 对于一个补充字符，utf8mb4 需要4个字节来存储它，而 utf8mb3 根本不能存储补充字符
  。当将 utf8mb3 列转换为 utf8mb4 时，您不必担心转换辅助字符，因为根本就没有任何
  补充字符。

在表结构方面，这些是主要的潜在的不兼容问题：
* 对于可变长度的字符数据类型（VARCHAR和TEXT类型），允许字符的最大长度 utf8mb4 列
  比 utf8mb3 列更少。
* 对于所有字符数据类型（CHAR、VARCHAR和TEXT类型），可供索引的字符的最大数量
  utf8mb4 列比 utf8mb3 列更少。

因此，为了将 utf8mb3 的表转换为 utf8mb4，可能需要改变一些列或索引定义。

可以使用 `ALTER TABLE` 把表从 utf8mb3 转换到 utf8mb4。假设一个表有这个定义：
```
CREATE TABLE t1 (
    col1 CHAR(10) CHARACTER SET utf8 COLLATE utf8_unicode_ci NOT NULL,
    col2 CHAR(10) CHARACTER SET utf8 COLLATE utf8_bin NOT NULL
) CHARACTER SET utf8;
```

下列语句将 t1 转换为使用 utf8mb4：
```
ALTER TABLE t1
DEFAULT CHARACTER SET utf8mb4,
MODIFY col1 CHAR(10)
CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL,
MODIFY col2 CHAR(10)
CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL;
```

从 utf8mb3 转换到 utf8mb4 要注意的是：列或索引键的最大长度按照字节数来说没有变化
，但按照字符数来说更少了，因为一个字符占用的最大长度变为了 4 个字节而不是 3 个字
节了。对于“CHAR”、“VARCHAR”和“TEXT”数据类型，在转换 MySQL 表时要注意这些问
题：
* 检查 utf8mb3 列的所有定义，并确保它们不会超过存储引擎的最大长度。
* 检查 utf8mb3 列上的所有索引，并确保它们不会超过存储引擎的最大长度。有时，由于
  存储引擎的增强，最大值可能会发生变化。

如果上述情况适用，您必须或者减少已定义的列或索引长度，或者继续使用 utf8mb3 而不
是 utf8mb4。

以下是一些可能需要进行结构调整的例子：

* 一个“TINYTEXT”列可以容纳255个字节，所以它可以容纳85个3字节或63个4字节的字符
  。假设您有一个使用 utf8mb3 的“TINYTEXT”列，但是必须能够包含超过63个字符。这
  种情况则您不能将其转换为 utf8mb4，除非您也将数据类型更改为更长的类型，如“TEXT
  ”。

  类似地，如果您想将列从 utf8mb3 转换为 utf8mb4，那么很长的“VARCHAR”列可能需要
  更改为更长的类型之一“TEXT”。

* 对于那些使用 `COMPACT` 或 `REDUNDANT` 行格式的表，InnoDB 的最大索引长度为 767
  字节，因此对于 utf8mb3 或 utf8mb4 列，您可以分别索引最多 255 或 191 个字符。如
  果您当前有 utf8mb3 列，索引长度超过 191 个字符，那么您必须索引较小数量的字符。

  在使用 `COMPACT` 或 `REDUNDANT` 行格式的 InnoDB 表中，这些列和索引定义是合法的：
    ```
    col1 VARCHAR(500) CHARACTER SET utf8, INDEX (col1(255))
    ```

  要使用 utf8mb4，索引必须更小：
    ```
    col1 VARCHAR(500) CHARACTER SET utf8mb4, INDEX (col1(191))
    ```

    注意：
    对于使用 `COMPRESSED` 或 `DYNAMIC` 行格式的 InnoDB 表，允许索引键前缀超过
    767 字节（最多可达 3072 字节）。用这些行格式创建的表格使您能够分别为 utf8mb3
    或 utf8mb4 列索引最多 1024 或 768 个字符。
    相关信息参考：Section 15.8.1.7, “Limits on InnoDB Tables”, and Section
    15.10.3, “DYNAMIC and COMPRESSED Row Formats”.

只有当您有非常长的列或索引时，才可能需要上述类型的更改。否则，您应该能够将
utf8mb3 的表格转换为 utf8mb4，而不会出现问题，使用前面所述的 `ALTER TABLE`。

以下项总结了其他潜在的不兼容问题：

* `SET NAMES 'utf8mb4'` 为连接字符集使用 4 字节字符集。只要不从服务器发送 4 字节
  字符，就不会出现问题。否则，期望每个字符最多接收 3 个字节的应用程序可能会出现
  问题。相反，期望发送 4 字节字符的应用程序必须确保服务器能够理解它们。

* 对于复制，如果在主服务器上使用支持补充字符的字符集，所有的从服务器也必须理解它
  们。

  另外，请记住，如果一个表在主服务器和从服务器有不同的定义，这可能会导致意外的结
  果。例如，在最大索引键长度上的差异使得在主服务器上使用 utf8mb3 而在从服务器上
  使用 utf8mb4 是有风险的。

如果您已经转换到 utf8mb4、utf16、utf16le 或 utf32，然后决定转换回 utf8mb3 或
ucs2（例如，降级到较老版本的 MySQL），这些考虑适用：

* utf8mb3 和 ucs2 的数据应该不会出现问题。

* 服务器必须足够新，能够识别您正在转换的字符集的定义。

* 对于引用 utf8mb4 字符集的对象定义，您可以在降级之前先用 mysqldump 将它们转储，
  编辑转储文件以更改 utf8mb4 的实例为 utf8，并在旧服务器上重新加载文件，只要数据
  中没有 4 字节字符。旧的服务器将在转储文件对象定义中看到 utf8，并创造使用（3 字
  节）utf8 字符集的新对象。
