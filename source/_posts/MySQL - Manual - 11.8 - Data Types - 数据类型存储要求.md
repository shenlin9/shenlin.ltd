---
title: MySQL - Manual - 11.8 - Data Types - 数据类型存储要求
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 数据类型存储要求

<!--more-->

磁盘上的表数据的存储需求取决于几个因素。不同的存储引擎表示数据类型和存储原始数据
的方式不同。表数据可能被压缩，无论是对于一个列还是一整行，都使表或列的存储需求的
计算变得更加复杂。

尽管磁盘上的存储布局存在差异，但用于通信和交换关于表行的信息的内部 MySQL APIs 使
用了适用于所有存储引擎的一致的数据结构。

这一节包括对 MySQL 支持的每种数据类型的存储需求的参考和信息，包括使用固定大小来
表示数据类型的存储引擎的内部格式和大小。信息是按类别或存储引擎列出的。

表的内部表示的最大行数为 65,535 字节，即使存储引擎能够支持更大的行。这个数字不包
括 BLOB 或 TEXT 列，它们只贡献了 9 到 12 个字节的大小。对于 BLOB 和 TEXT 数据，
信息存储在内存的不同区域内，而不是行缓冲区。不同的存储引擎根据它们处理相应类型的
方法以不同的方式处理这些数据的分配和存储。有关更多信息，请参阅 Chapter 16,
Alternative Storage Engines, and Section C.10.4, “Limits on Table Column Count
and Row Size”。

## InnoDB Table Storage Requirements

参考 Section 15.8.1.2, “The Physical Row Structure of an InnoDB Table” 查看
InnoDB 表存储需求的信息

## Numeric Type Storage Requirements

    Data Type               Storage Required
    --------------------------------
    TINYINT                     1 byte
    SMALLINT                    2 bytes
    MEDIUMINT                   3 bytes
    INT, INTEGER                4 bytes
    BIGINT                      8 bytes
    FLOAT(p)                    4 bytes if 0 <= p <= 24, 8 bytes if 25 <= p <= 53
    FLOAT                       4 bytes
    DOUBLE [PRECISION], REAL    8 bytes
    BIT(M) approximately        (M+7)/8 bytes
    DECIMAL(M,D), NUMERIC(M,D) Varies;  see following discussion

Values for `DECIMAL` (and `NUMERIC`) columns are represented using a binary
format that packs nine decimal (base 10) digits into four bytes. Storage for the
integer and fractional parts of each value are determined separately. Each
multiple of nine digits requires four bytes, and the “leftover” digits require
some fraction of four bytes. The storage required for excess digits is given by
the following table.
`DECIMAL`（和 `NUMERIC`）列的值用二进制格式表示，二进制格式把 9 个十进制（基数
10）位打包成 4 个字节。每个值的整数和小数部分的存储是分开确定的。每一个 9 位数的
倍数都需要 4 个字节，而“剩余（leftover）”数字需要 4 个字节的一小部分。下表给出
了多余数字所需的存储。

    Leftover Digits     Number of Bytes
    -------------------------------
    0                        0
    1                        1
    2                        1
    3                        2
    4                        2
    5                        3
    6                        3
    7                        4
    8                        4

## Date and Time Type Storage Requirements

对于 `TIME`、`DATETIME` 和 `TIMESTAMP` 列，MySQL 5.6.4 之前版本和之后版本创建表
所需要的存储空间不同。这是由于版本 5.6.4 的变化：允许这些类型有一个小数部分，需
要 0 到 3 字节。

    Data Type   Storage Required        Storage Required 
                Before MySQL 5.6.4      as of MySQL 5.6.4
    --------    --------                ------
    YEAR        1 byte                   1 byte
    DATE        3 bytes                  3 bytes
    TIME        3 bytes                  3 bytes + fractional seconds storage
    DATETIME    8 bytes                  5 bytes + fractional seconds storage
    TIMESTAMP   4 bytes                  4 bytes + fractional seconds storage

在 MySQL 5.6.4 中， `YEAR` 和 `DATE` 的存储空间保持不变。然而， `TIME`、
`DATETIME` 和 `TIMESTAMP` 的表示则不同。 `DATETIME` 被打包地更高效，非小数部分只
需要 5 个而非 8 个字节，并且这三个数据类型都有一个小数部分，取决于存储值的分秒精
度，需要 0 到 3 字节存储空间。

    Fractional Seconds      Precision Storage Required
    0                           0 bytes
    1, 2                        1 byte
    3, 4                        2 bytes
    5, 6                        3 bytes

例如, `TIME(0)`, `TIME(2)`, `TIME(4)`, `TIME(6)` 分别使用 3, 4, 5, 6 字节的存储
空间，`TIME` 和 `TIME(0)` 等效,需要相同的存储空间。

有关时态值的内部表示的具体细节信息，请参阅：
![MySQL Internals: Important Algorithms and Structures](https://dev.mysql.com/doc/internals/en/algorithms.html)

## String Type Storage Requirements

下面的表中，`M` 表示声明的列长度，非二进制字符串类型按字符计算，二进制字符串类型
按字节计算。`L` 表示给定字符串值的实际字节长度。

    Data Type                   Storage Required
    ----                        ----
    CHAR(M)                     为可变长度字符集格式优化存储的 InnoDB 行紧凑家族，
                                The compact family of InnoDB row formats
                                optimize storage for variable-length character
                                sets. See “15.8.1.2 The Physical Row Structure
                                of an InnoDB Table --- COMPACT Row Format
                                Characteristics”. 
                                否则, M × w bytes, 0 <= M <= 255, w 是字符集中最
                                大长度字符所需的字节数

    BINARY(M)                   M bytes, 0 <= M <= 255
    VARCHAR(M), VARBINARY(M)    L + 1 bytes 如果列值需要 0 − 255 bytes
                                L + 2 bytes 如果列值需要大于 255 bytes
    TINYBLOB, TINYTEXT          L + 1 bytes, where L < 2^8
    BLOB, TEXT                  L + 2 bytes, where L < 2^16
    MEDIUMBLOB, MEDIUMTEXT      L + 3 bytes, where L < 2^24
    LONGBLOB, LONGTEXT          L + 4 bytes, where L < 2^32

    ENUM('value1','value2',...) 1 或 2 bytes, 取决于枚举值的数量 (最多 65,535 个
                                值)

    SET('value1','value2',...)  1, 2, 3, 4, 或 8 bytes, 取决于集合成员的数量 (最
                                多 64 个成员)

可变长度字符串类型是使用长度前缀加上数据存储的。根据数据类型，长度前缀需要 1 到
4 个字节，前缀的值是字符串的字节长度 `L`。例如，存储 `MEDIUMTEXT` 值需要 `L`
字节存储值本身再加上三个字节来存储值的长度。

要计算用来存储特定 `CHAR`、`VARCHAR` 或 `TEXT` 列值的字节数，您必须考虑到该列所
使用的字符集，以及该值是否包含多字节字符。特别地，当使用 `utf8` Unicode 字符集时
，您必须记住，并不是所有字符都使用相同的字节数。 `utf8mb3` 和 `utf8mb4` 字符集可
以分别要求每个字符最多 3 个字节和 4 个字节。对于不同类别的 `utf8mb3` 或
`utf8mb4` 字符的存储，请参阅 Section 10.9, “Unicode Support”。

`VARCHAR`, `VARBINARY`, `BLOB` 和 `TEXT` 类型是可变长度类型，每个的存储需求取决
于这些因素：
* 列值的实际长度
* 列的最大可能长度
* 用于此列的字符集，因为某些字符集包含多字节字符

例如，一个 `VARCHAR(255)` 列可以容纳最大长度为 255 个字符的字符串。假设此列使用
`latin1` 字符集（每个字符一个字节），实际需要的存储空间是字符串的长度（L），加上
记录字符串长度的一个字节。对于字符串 'abcd'，L 是 4，存储需求是 5 字节。如果同一
列被声明为使用 `ucs2` 双字节字符集，那么储存需求是 10 字节：abcd 的长度是 8 字节
，并且此列需要两个字节来存储长度，因为列的最大长度大于 255（最多可达 510 字节）。

储存在 `VARCHAR` 或 `VARBINARY` 列中的有效最大字节数受制于最大行数 65,535 字节，
这在所有列中都是共享的。对于储存多字节字符的 `VARCHAR` 列来说，字符的有效最大数
量较少。例如，`utf8mb4` 的每个字符最多需要 4 个字节，所以使用 `utf8mb4` 字符集的
`VARCHAR` 列可以被声明为最多 16,383 个字符。请参阅 Section C.10.4, “Limits on
Table Column Count and Row Size”。

InnoDB 将大于或等于 768 字节的固定长度字段编码为可变长度字段，这些可变长度字段可
以存储在页面之外。例如，如果字符集的最大字节长度大于 3，那么 `CHAR(255)` 的列可
以超过 768 字节，就像 `utf8mb4` 一样。

`ENUM` 对象的大小是由不同枚举值的数量决定的。一个字节用于枚举，最多有 255 个可能
的值。两个字节用于枚举，在 256 和 65,535 个可能的值之间。参见 Section 11.4.4, “
The ENUM Type”。

一个 `SET` 对象的大小由不同的集合成员的数量决定。如果集合大小为 N，则对象占据
`(N+7)/8` 字节，四舍五入为 1、2、3、4 或 8 字节。一个 `SET` 最多可以有 64 个成员
。参见 Section 11.4.5, “The SET Type”。

## Spatial Type Storage Requirements

MySQL stores geometry values using 4 bytes to indicate the SRID followed by the
WKB representation of the value. 
MySQL 使用 4 个字节来存储几何值，以指示 SRID 后跟 WKB 的值表示。`LENGTH()` 函数
返回值存储所需的字节空间。

关于空间值的 WKB 和内部存储格式的描述，参见 Section 11.5.3, “Supported Spatial
Data Formats”。

## JSON Storage Requirements

that is, the space consumed by a JSON document is roughly the same as it would
be for the document's string representation stored in a column of one of these
types. However, there is an overhead imposed by the binary encoding, including
metadata and dictionaries needed for lookup, of the individual values stored in
the JSON document. For example, a string stored in a JSON document requires 4 to
10 bytes additional storage, depending on the length of the string and the size
of the object or array in which it is stored.
一般来说，JSON 列的存储需求与 `LONGBLOB` 或 `LONGTEXT` 列大致相同；也就是说，
JSON 文档所消耗的空间与存储在这些类型列中的文档字符串表示大致相同。然而，二进制
编码所带来的开销，包括用于查找的元数据和字典，以及存储在 JSON 文档中的个别值。例
如，存储在 JSON 文档中的字符串需要 4 到 10 字节的额外存储，这取决于字符串的长度
和存储的对象或数组的大小。

此外，MySQL 对存储在 JSON 列中的任何 JSON 文档的大小都有限制，这样它就不能比
`max_allowed_packet` 的值大。

