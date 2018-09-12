---
title: MySQL - Manual - 10.7 - 字符集：列字符集转换 - Column Character Set Conversion
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 字符集：列字符集转换

<!--more-->

要转换二进制或非二进制字符串列来使用特定字符集，请使用 `ALTER TABLE`。为了成功地
进行转换，必须适用以下条件之一：

* 如果此列是二进制数据类型（BINARY、VARBINARY、BLOB），那么此列的所有值都必须使
  用单个字符集（您正在将列转换到的字符集）进行编码。如果您使用多个字符集在二进制
  列中存储信息，那么 MySQL 就无法知道哪个值使用哪个字符集，就不能正确地转换数据
  。

* 如果列是非二进制数据类型(CHAR、VARCHAR、TEXT)，其内容编码应该使用列的字符集，
  而不是其他字符集。如果内容使用了不同的字符集，您可以先将列转换为使用二进制数据
  类型，然后再转换为使用所需字符集的非二进制列。

假设表 `t` 有一个名为 `col1` 的二进制列，定义为 `VARBINARY(50)`。假设列中的信息
是使用单个字符集进行编码的，您可以将其转换为使用那个字符集的非二进制列。例如，如
果 `col1` 包含用希腊字符集表示的二进制数据，您可以将其转换为如下：
```
ALTER TABLE t MODIFY col1 VARCHAR(50) CHARACTER SET greek;
```

如果列的原始类型是 `BINARY(50)`，您可以将其转换为 `CHAR(50)`，但是最终的值将会在
末尾加上 `0x00` 字节，这可能是不需要的。要删除这些字节，请使用 `TRIM()` 函数：
```
UPDATE t SET col1 = TRIM(TRAILING 0x00 FROM col1);
```

假设表 `t` 有一个非二进制列 `col1` 定义为 `CHAR(50) CHARACTER SET latin1`，但你
想要把它转换为 `utf8`，就可以存储多种语言的值。可以用下列语句完成：
```
ALTER TABLE t MODIFY col1 CHAR(50) CHARACTER SET utf8;
```

如果列包含的字符有的不是同时在两个字符集中，那么转换可能是有损的。如果你有 MySQL
4.1 之前的旧表，其中的非二进制列包含的值实际上是用与服务器默认字符集不同的字符集
进行编码的，那么就会出现一个特殊的情况。

例如，一个应用程序可能在列中存储了 `sjis` 值，即使 MySQL 的默认字符集是不同的。
可以将此列转换为使用适当的字符集，但是需要额外的步骤。

假设服务器的默认字符集是 `latin1`，而 `col1` 被定义为 `CHAR(50)`，但其内容是
`sjis` 值。第一步是将列转换为二进制数据类型，它删除现有的字符集信息，而不执行任
何字符转换：
```
ALTER TABLE t MODIFY col1 BLOB;
```

接下来的步骤是将列转换成具有适当字符集的非二进制数据类型：
```
ALTER TABLE t MODIFY col1 CHAR(50) CHARACTER SET sjis;
```

这个过程要求在升级到 MySQL 4.1 或更高版本之后，表没有被如 `INSERT` 或 `UPDATE`
一类的语句修改过。因为那种情况下，MySQL 会使用 `latin1` 在列中存储新值，而列将包
含 `sjis` 和 `latin1` 值的混合，不能正确地转换。

如果您在最初创建列时指定了某些属性，那么您也应该在用 `ALTER TABLE` 修改表时指定
它们。例如，如果您指定 `NOT NULL` 和显式的 `DEFAULT` 值，您也应该在 `ALTER
TABLE` 语句中提供它们。否则，产生的列定义将不包括这些属性。

要转换一个表里的所有字符列，`ALTER TABLE ... CONVERT TO CHARACTER SET charset`
可能有用，参考：Section 13.1.8, “ALTER TABLE Syntax”.
