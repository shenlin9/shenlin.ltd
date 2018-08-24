---
title: MySQL - Manual - 9.6 - 语言结构：注释语法 - Comment Syntax
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 语言结构：注释语法

<!--more-->

MySQL 服务器支持 3 种注释：

* 从一个 `#` 字符到行尾

* 从一个 `--` 序列到行尾。在 MySQL 里, `--` 注释格式要求第二个横线后面必须跟着最
  少一个空白符或者控制字符（如空格、tab、换行等），这种语法和标准的 SQL 注释语法
  稍有不同，标准注释语法描述参考：Section 1.8.2.4, “'--' as the Start of a
  Comment”.

* 使用 `/* sequence to the following */` 序列, 就像在 C 语言里一样，这种语法支持
  跨行注释，因为注释的开始和结束不必在同一行。

3 种注释演示：
```
mysql> SELECT 1+1;  # This comment continues to the end of line
mysql> SELECT 1+1;  -- This comment continues to the end of line
mysql> SELECT 1     /* this is an in-line comment */ + 1;
mysql> SELECT 1+
/*
this is a
multiple-line comment
*/
1;
```

不支持嵌套注释，一些儿情况下可能支持，但通常不支持，用户应该避免使用潜逃注释。

MySQL 服务器支持 C 风格注释的一些变体，使得您能够编写包含 MySQL 扩展的代码，但是
仍然可以移植，通过使用以下形式的注释：
```
/*! MySQL-specific code */
```

下面例子中，MySQL 服务器就像处理任何其他 SQL 语句一样解释并执行注释里的代码
`STRAIGHT_JOIN`，但是其他的 SQL 服务器会忽略注释里的代码：
```
SELECT /*! STRAIGHT_JOIN */ col1 FROM table1,table2 WHERE ...
```

如果在 `!` 符号后添加版本号，则只有 MySQL 的版本号大于或等于指定的版本号时，才会
执行注释里的语句，如下列只有 MySQL 5.1.10 或更高版本才会执行 `KEY_BLOCK_SIZE`：
```
CREATE TABLE t1(a INT, KEY (a)) /*!50110 KEY_BLOCK_SIZE=1024 */;
```

刚才描述的注释语法适用于 `mysqld` 服务器解析 SQL 语句的方式。`mysql` 客户端程序
在将语句发送到服务器之前也对语句进行一些解析。（它这样做是为了确定一个多语句输入
行内的语句边界。）

这种格式的注释：`/*!12345 ... */` 不是保存在服务器上的，如果使用这种格式来注释存
储例程，注释不会保留在服务器上。？？？

C 风格的注释语法的另一种变体用于指定优化器提示。提示注释在注释开始符号 `/*` 后面
跟着一个 `+` 字符，例子:
```
SELECT /*+ BKA(t1) */ FROM ... ;
```
更多信息参考：Section 8.9.2, “Optimizer Hints”.

不支持在短格式的 mysql 命令如 `\C` 中使用多行注释 `/* ... */`
