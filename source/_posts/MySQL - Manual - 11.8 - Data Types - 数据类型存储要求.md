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

For `TIME`,`DATETIME` and `TIMESTAMP` columns, the storage required for tables created before MySQL 5.6.4 differs from tables created from 5.6.4 on. This is due to a change in 5.6.4 that permits these types to have a fractional part, which requires from 0 to 3 bytes.

    Data Type   Storage Required        Storage Required 
                Before MySQL 5.6.4      as of MySQL 5.6.4
    --------    --------                ------
    YEAR        1 byte                   1 byte
    DATE        3 bytes                  3 bytes
    TIME        3 bytes                  3 bytes + fractional seconds storage
    DATETIME    8 bytes                  5 bytes + fractional seconds storage
    TIMESTAMP   4 bytes                  4 bytes + fractional seconds storage

As of MySQL 5.6.4, storage for YEAR and DATE remains unchanged. However, TIME, DATETIME, and
TIMESTAMP are represented differently. DATETIME is packed more efficiently, requiring 5 rather than 8
bytes for the nonfractional part, and all three parts have a fractional part that requires from 0 to 3 bytes,
depending on the fractional seconds precision of stored values.

    Fractional Seconds Precision Storage Required
    0 0 bytes
    1, 2 1 byte
    3, 4 2 bytes
    5, 6 3 bytes

For example, TIME(0), TIME(2), TIME(4), and TIME(6) use 3, 4, 5, and 6 bytes, respectively. TIME
and TIME(0) are equivalent and require the same storage.
For details about internal representation of temporal values, see MySQL Internals: Important Algorithms
and Structures.

## String Type Storage Requirements

In the following table, M represents the declared column length in characters for nonbinary string types and
bytes for binary string types. L represents the actual length in bytes of a given string value.

    Data Type               Storage Required
    CHAR(M)                     The compact family of InnoDB row formats optimize storage
                                for variable-length character sets. See COMPACT Row Format
                                Characteristics. Otherwise, M × w bytes, <= M <= 255, where
                                w is the number of bytes required for the maximum-length
                                character in the character set.
    BINARY(M)                   M bytes, 0 <= M <= 255
    VARCHAR(M), VARBINARY(M)    L + 1 bytes if column values require 0 − 255 bytes, L + 2 bytes if values may require more than 255 bytes
    TINYBLOB, TINYTEXT          L + 1 bytes, where L < 2 8
    BLOB, TEXT                  L + 2 bytes, where L < 2 16
    MEDIUMBLOB, MEDIUMTEXT      L + 3 bytes, where L < 2 24
    LONGBLOB, LONGTEXT          L + 4 bytes, where L < 2 32
    ENUM('value1','value2',...) 1 or 2 bytes, depending on the number of enumeration values (65,535 values maximum)
    SET('value1','value2',...)  1, 2, 3, 4, or 8 bytes, depending on the number of set members (64 members maximum)

Variable-length string types are stored using a length prefix plus data. The length prefix requires from one
to four bytes depending on the data type, and the value of the prefix is L (the byte length of the string). For
example, storage for a MEDIUMTEXT value requires L bytes to store the value plus three bytes to store the
length of the value.

To calculate the number of bytes used to store a particular CHAR, VARCHAR, or TEXT column value, you
must take into account the character set used for that column and whether the value contains multibyte
characters. In particular, when using a utf8 Unicode character set, you must keep in mind that not all
characters use the same number of bytes. utf8mb3 and utf8mb4 character sets can require up to three
and four bytes per character, respectively. For a breakdown of the storage used for different categories of
utf8mb3 or utf8mb4 characters, see Section 10.9, “Unicode Support”.

VARCHAR, VARBINARY, and the BLOB and TEXT types are variable-length types. For each, the storage
requirements depend on these factors:
* The actual length of the column value
* The column's maximum possible length
* The character set used for the column, because some character sets contain multibyte characters

For example, a VARCHAR(255) column can hold a string with a maximum length of 255 characters.
Assuming that the column uses the latin1 character set (one byte per character), the actual storage
required is the length of the string (L), plus one byte to record the length of the string. For the string
'abcd', L is 4 and the storage requirement is five bytes. If the same column is instead declared to use the
ucs2 double-byte character set, the storage requirement is 10 bytes: The length of 'abcd' is eight bytes
and the column requires two bytes to store lengths because the maximum length is greater than 255 (up to
510 bytes).

The effective maximum number of bytes that can be stored in a VARCHAR or VARBINARY column is subject
to the maximum row size of 65,535 bytes, which is shared among all columns. For a VARCHAR column that
stores multibyte characters, the effective maximum number of characters is less. For example, utf8mb4
characters can require up to four bytes per character, so a VARCHAR column that uses the utf8mb4
character set can be declared to be a maximum of 16,383 characters. See Section C.10.4, “Limits on Table
Column Count and Row Size”.

InnoDB encodes fixed-length fields greater than or equal to 768 bytes in length as variable-length fields,
which can be stored off-page. For example, a CHAR(255) column can exceed 768 bytes if the maximum
byte length of the character set is greater than 3, as it is with utf8mb4.

The size of an ENUM object is determined by the number of different enumeration values. One byte is used
for enumerations with up to 255 possible values. Two bytes are used for enumerations having between
256 and 65,535 possible values. See Section 11.4.4, “The ENUM Type”.

The size of a SET object is determined by the number of different set members. If the set size is N, the
object occupies (N+7)/8 bytes, rounded up to 1, 2, 3, 4, or 8 bytes. A SET can have a maximum of 64
members. See Section 11.4.5, “The SET Type”.

## Spatial Type Storage Requirements

MySQL stores geometry values using 4 bytes to indicate the SRID followed by the WKB representation of
the value. The LENGTH() function returns the space in bytes required for value storage.

For descriptions of WKB and internal storage formats for spatial values, see Section 11.5.3, “Supported
Spatial Data Formats”.

## JSON Storage Requirements

In general, the storage requirement for a JSON column is approximately the same as for a LONGBLOB or
LONGTEXT column; that is, the space consumed by a JSON document is roughly the same as it would be
for the document's string representation stored in a column of one of these types. However, there is an
overhead imposed by the binary encoding, including metadata and dictionaries needed for lookup, of the
individual values stored in the JSON document. For example, a string stored in a JSON document requires
4 to 10 bytes additional storage, depending on the length of the string and the size of the object or array in
which it is stored.

In addition, MySQL imposes a limit on the size of any JSON document stored in a JSON column such that it
cannot be any larger than the value of max_allowed_packet.
