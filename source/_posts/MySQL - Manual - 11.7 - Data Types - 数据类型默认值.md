---
title: MySQL - Manual - 11.7 - Data Types - 数据类型默认值
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 数据类型默认值

<!--more-->

数据类型规范可以有显式或隐式缺省值。

数据类型规范中的 `DEFAULT value` 子句显式地为一个列指示缺省值。

例子：
```
CREATE TABLE t1 (
    i INT DEFAULT -1,
    c VARCHAR(10) DEFAULT '',
    price DOUBLE(16,2) DEFAULT 0.00
);
```

`SERIAL DEFAULT VALUE` 是一个特例。在整数列的定义中，它是 `NOT NULL
AUTO_INCREMENT UNIQUE` 的别名。

显式 `DEFAULT` 子句处理的某些方面是依赖于版本的，如下所述。

## Handling of Explicit Defaults as of MySQL 8.0.13

### 默认值两种形式

`DEFAULT` 子句中指定的默认值可以是字面常量或表达式。但表达式默认值应包含在括号内
，以便将它们与字面常量默认值区分开来。例子:
```
CREATE TABLE t1 (
    -- literal defaults
    i INT DEFAULT 0,
    c VARCHAR(10) DEFAULT '',

    -- expression defaults
    f FLOAT DEFAULT (RAND() * RAND()),
    b BINARY(16) DEFAULT (UUID_TO_BIN(UUID())),
    d DATE DEFAULT (CURRENT_DATE + INTERVAL 1 YEAR),
    p POINT DEFAULT (Point(0,0)),
    j JSON DEFAULT (JSON_ARRAY())
);
```
但有一个例外：对于 `TIMESTAMP` 和 `DATETIME` 列，您可以指定 `CURRENT_TIMESTAMP`
函数作为其默认值，而不使用圆括号。参考 Section 11.3.5, “Automatic
Initialization and Updating for TIMESTAMP and DATETIME”.

### 有些类型默认值必须写为表达式

只有当值被写成表达式形式时，即使表达式值是一个字面量，`BLOB`、`TEXT`、`GEOMETRY`
和 `JSON` 数据类型才会被赋值：

* 这是允许的（字面量默认值指定为表达式）：
```
CREATE TABLE t2 (b BLOB DEFAULT ('abc'));
```

* 这会出错（字面量默认值没有指定为表达式）：
```
CREATE TABLE t2 (b BLOB DEFAULT 'abc');
```

### 表达式默认值规则

表达式默认值必须遵守以下规则。如果一个表达式包含不允许的结构，就会出现错误。
* 字面量、内置函数（包括确定性和非确定性）和操作符都是允许的。
* 子查询、参数、变量、存储函数和用户自定义函数是不允许的。
* 一个表达式的默认值不能依赖于具有 `AUTO_INCREMENT` 属性的列。
* 一列的表达式默认值可以引用其他表列，但例外情况是，对生成的列或带有表达式默认值
  的列的引用必须是表定义中较早出现的列。也就是说，表达式默认值不能向前引用生成的
  列或带有表达式默认值的列。

  排序约束也适用于通过 `ALTER TABLE` 对表列重新排序的情况。如果结果表有一个表达
  式默认值，向前引用了带表达式默认值的列或生成的列，那么语句就失败了。

    注意
    如果表达式默认值的任何组件都依赖于 SQL 模式，那么对于表的不同用法可能会出现
    不同的结果，除非在所有使用过程中 SQL 模式都是相同的。

对于 `CREATE TABLE ... LIKE` 和 `CREATE TABLE ... SELECT`，目标表保留原始表中的
表达式默认值。

如果表达式默认值涉及非确定性函数（a nondeterministic function），那么对于基于语
句的复制来说，任何导致表达式被求值的语句都是不安全的。这包括诸如 `INSERT`，
`UPDATE` 和 `ALTER TABLE` 这样的语句。

当插入新行时，可以通过省略列名或指定列为 `DEFAULT`（就像带有字面量默认值的列一样
）来插入带有表达式默认值的列。
```
mysql> CREATE TABLE t4 (uid BINARY(16) DEFAULT (UUID_TO_BIN(UUID())));
mysql> INSERT INTO t4 () VALUES();
mysql> INSERT INTO t4 () VALUES(DEFAULT);
mysql> SELECT BIN_TO_UUID(uid) AS uid FROM t4;
+--------------------------------------+
| uid |
+--------------------------------------+
| f1109174-94c9-11e8-971d-3bf1095aa633 |
| f110cf9a-94c9-11e8-971d-3bf1095aa633 |
+--------------------------------------+
```
然而，使用 `DEFAULT(col_name)` 为指定列指定默认值只允许用于具有字面量默认值的列
，不适用于具有表达式默认值的列。

并不是所有的存储引擎都允许表达式默认值。对于那些不允许这样做的，会出现了一个
`ER_UNSUPPORTED_ACTION_ON_DEFAULT_VAL_GENERATED` 错误。

如果默认值的数据类型评估与列声明类型不同，就会根据通常的 MySQL 类型转换规则把默
认值隐式强制转换为声明类型。参考 Section 12.2, “Type Conversion in Expression
Evaluation”.

## Handling of Explicit Defaults Prior to MySQL 8.0.13

一个例外，`DEFAULT` 子句中指定的默认值必须是一个字面量常量，不能是一个函数或一个
表达式。这意味着，例如您不能将日期列的缺省值设置为诸如 `NOW()` 或 `CURRENT_DATE`
这样的函数的值。例外情况是，对于 `TIMESTAMP` 和 `DATETIME` 列，您可以指定
`CURRENT_TIMESTAMP` 作为缺省值。See Section 11.3.5, “Automatic Initialization
and Updating for `TIMESTAMP` and DATETIME”。

`BLOB`,`TEXT`,`GEOMETRY` 和 `JSON` 数据类型不能被分配默认值。

如果默认值的数据类型评估与列声明类型不同，就会根据通常的 MySQL 类型转换规则把默
认值隐式强制转换为声明类型。参考 Section 12.2, “Type Conversion in Expression
Evaluation”.

## Handling of Implicit Defaults

如果数据类型规范没有包含显式的 `DEFAULT`，那么 MySQL 将确定默认值如下：

如果列可以用 `NULL` 值，则 MySQL 定义此列使用显式的 `DEFAULT NULL` 子句。

如果列不可以用 `NULL` 值，则 MySQL 定义此列不使用显式的 `DEFAULT NULL` 子句。例
外：如果列定义为 `PRIMARY KEY` 但没有显式的 `NOT NULL`，则 MySQL 仍创建此列为
`NOT NULL` 列（因为 `PRIMARY KEY` 列必须是 `NOT NULL`）。

对于没有显式的 `DEFAULT` 子句的 `NOT NULL` 列中的数据，如果 `INSERT` 或
`REPLACE` 语句不包含此列的值，或者 `UPDATE` 语句将此列设为 `NULL` ，MySQL 根据当
时实际的 `SQL` 模式处理列：

* 如果启用了严格的 SQL 模式，事务表就会出现错误，并回滚语句。对于非事务性表，会
  发生错误，但是如果这发生在多行语句的第二行或后续行中，则前面的行仍会被插入。

* 如果没有启用严格的模式，MySQL 将此列设置为列数据类型的隐式默认值。

假如表 `t` 定义如下：
```
CREATE TABLE t (i INT NOT NULL);
```

在这个例子里，`i` 没有显式的默认值，因此在严格的模式下，下列语句中的每一个语句都
会产生一个错误不会插入任何一行。当不使用严格模式时，只有第三个语句产生错误，隐式
的默认值被插入到前两个语句中，但是第三个失败是因为 `DEFAULT(i)` 不能产生一个值：
```
INSERT INTO t VALUES();
INSERT INTO t VALUES(DEFAULT);
INSERT INTO t VALUES(DEFAULT(i));
```
See Section 5.1.10, “Server SQL Modes”.

对于一个给定的表，`SHOW CREATE TABLE` 语句可以显示那些列使用了显式的 `DEFAULT`
子句。

隐式的默认值定义如下：

* 对于数值类型，默认值为 0，例外是：对于用 `AUTO_INCREMENT` 属性声明的整数或浮点
  类型，默认值是序列中下一个值。

* 对于除了 `TIMESTAMP` 之外的日期和时间类型，默认值是该类型的适当“零”值。如果
  启用了 `explicit_defaults_for_timestamp` 系统变量，那么 `TIMESTAMP` 也是如此（
  参见 Section 5.1.7, “Server System Variables”）。否则，对于表格中的第一个
  `TIMESTAMP` 列，默认值是当前日期和时间。参见 Section 11.3, “Date and Time
  Types”。

* 对于除了 `ENUM` 之外的字符串类型，默认值是空字符串。对于 `ENUM` 来说，默认值是
  第一个枚举值。

