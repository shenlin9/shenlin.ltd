---
title: MySQL - 数据类型
categories: 
 - MySQL
tags: 
 - MySQL
---

MySQL 数据类型

<!--more-->

## 数据类型

### 数值型

整数可按十进制形式或十六进制形式表示，十六进制以 `0x` 开头

十六进制数字不区分大小写，但其前缀 `0x` 不能为 `0X`。即 0x0a 和 0x0A 都是合
法的，但 0X0a 和 0X0A 不是合法的

科学计数法：1.34E+12 和 43.27e-1 都是合法的科学表示法表示的数。而1.34E12 不是合法的，因为指数前的符号未给出，因为`e`是一个合法的十六进制数字，会被误认为十六进制数

### 字符串型

可用单引号或双引号将值引起来

如果字符串里要包括引号，则有下面三种选择

1. 相同的引号，则双写即可
    'I can''t do it'
    "He said,""I told you so"" "

2. 不相同的引号，则无需处理
    "I can't do it"
    'He said,"I told you so" '

3. 反斜杠转义，无需关心引号是否相同
    'I can\'t do it'
    "I can\'t do it"
    "He said,\"I told you so\" "
    'He said,\"I told you so\" '

转义序列

    \0  NUL (ASCII 0)
    \'  单引号
    \"  双引号
    \b  退格
    \n  新行
    \r  回车
    \t  制表符
    \\  反斜杠

    注意 NUL 字节与 NULL 值不同；NUL 为一个零值字节，而 NULL 为没有值。

可以用十六进制常数来表示字符串，这时十六进制数字被看作 ASCII 码并转换为字符

### 日期和时间型

"1999-06-17"
"12:30:43"
"1999-06-17 12:30:43"

DATE_FORMAT()函数可以任意形式显示日期

### NULL 值

表示无类型

## 列类型

MySQL 的列类型都有如下特性：
* 可以存放什么类型的值
* 值要占据多少空间，值是定长的还是变长的
* 此类型的值怎样比较和处理
* 此类型是否允许 NULL 值
* 此类型是否可以索引

### 数值列类型

TINYINT
SMALLINT
MEDIUMINT
INT
BIGINT
FLOAT
DOUBLE
DECIMAL

### 字符串列类型

CHAR
VARCHAR
TINYBLOB
BLOB
MEDIUMBLOB
LONGBLOB
TINYTEXT
MEDIUMTEXT
LONGTEXT
ENUM
SET

### 日期和时间列类型

DATE
TIME
DATETIME
TIMESTAMP
YEAR
