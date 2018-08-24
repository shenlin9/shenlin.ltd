---
title: MySQL - Manual - 9.4 - 语言结构：用户自定义变量 - User-Defined Variables
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 语言结构：用户自定义变量

<!--more-->

你可以在一个语句中存储一个用户定义的变量，然后在另一个语句中引用它。这使您能够将
值从一个语句传递到另一个语句。

用户变量形式：`@var_name`，变量名 `var_name` 有字母、数字、英文句号 `.`、下划线
`_` 和美元符号 `$` 组成。如果你将它作为字符串或标识符引用，用户变量名还可以包含
其他字符，例如：`@'my-var'`, `@"my-var"`, “@`my-var`”

用户自定义的变量是特定于 Session 会话的。由一个客户端定义的用户变量不能被其他客
户端看到或使用。（例外：有权访问性能模式表 `user_variables_by_thread` 的用户可以
看到所有 Session 会话的所有用户变量。）当客户端退出时，给定客户端会话的所有变量
都会自动释放。

用户变量名不区分大小写。变量名的最大长度为 64 个字符。

设置用户定义变量的一种方法是通过 `SET` 语句：
```
SET @var_name = expr [, @var_name = expr] ...
```
对于 `SET` 语句，`=` 或 `:=` 都可以用作赋值运算符。

也可以在非 `SET` 语句里为用户变量赋值，这种情况下赋值运算符必须为 `:=` 不能是
`=`，因为在非 `SET` 语句里，后者是作为比较操作符判断是否相等的：
```
mysql> SET @t1=1, @t2=2, @t3:=4;
mysql> SELECT @t1, @t2, @t3, @t4 := @t1+@t2+@t3;
+------+------+------+--------------------+
| @t1   | @t2   | @t3   | @t4 := @t1+@t2+@t3|
+------+------+------+--------------------+
| 1     | 2     | 4     | 7                 |
+------+------+------+--------------------+
```

用户变量可以从一组有限的数据类型中为其分配一个值：整数、小数、浮点数、二进制或非
二进制串，或者 `NULL` 值。小数和实数的赋值并不保留值的精度或数值范围。值的类型如
果不是被允许的类型则转换成允许的类型。例如，具有时间或空间数据类型的值（a value
having a temporal or spatial data type）被转换成二进制串。具有 `JSON` 数据类型的
值被转换成带有 `utf8mb4` 字符集的字符串，并使用 `utf8mb4_bin` 校对规则。

如果一个用户变量被分配一个非二进制（字符）字符串值，那么它就具有与字符串相同的字
符集和校对规则。用户变量的可强制性（coercibility）是隐式的。（这与表列值相同。）

> 注：关于 coercibility，指的是校对规则冲突时，表达式会从较底的强制性转换到较高
> 的强制性，较低的强制性具有较高的优先级，参考：
> https://stackoverflow.com/questions/1438103/what-does-coercibility-mean-mysql-user-variables

分配给用户变量的十六进制值或位值被当作二进制字符串处理。为了将一个十六进制值或位
值作为一个数字分配给一个用户变量，则需要在数值上下文中使用它。例如，加 0 或使用
`CAST(... AS UNSIGNED)`:
```
mysql> SET @v1 = X'41';
mysql> SET @v2 = X'41'+0;
mysql> SET @v3 = CAST(X'41' AS UNSIGNED);
mysql> SELECT @v1, @v2, @v3;
+------+------+------+
| @v1 | @v2 | @v3 |
+------+------+------+
| A | 65 | 65 |
+------+------+------+

mysql> SET @v1 = b'1000001';
mysql> SET @v2 = b'1000001'+0;
mysql> SET @v3 = CAST(b'1000001' AS UNSIGNED);
mysql> SELECT @v1, @v2, @v3;
+------+------+------+
| @v1 | @v2 | @v3 |
+------+------+------+
| A | 65 | 65 |
+------+------+------+
```

如果在结果集中选择了用户变量的值，那么它就会作为字符串返回给客户端。

如果你引用一个未被初始化的变量，它的值为 NULL，数据类型是字符串。

用户变量可以在大多数允许表达式的上下文中使用。这目前还不包括显式地要求文字值的上
下文，比如“SELECT”语句的“LIMIT”子句，或者“LOAD DATA”语句中的“IGNORE N
LINES”子句。

作为一般规则，除了“SET”语句，永远不应该为用户变量赋值后在同一个语句中读出值。
例如，为了增加一个变量，这是可以的：
```
SET @a = @a + 1;
```

对于其他的语句，比如“SELECT”，可能会得到您期望的结果，但这并不能得到保证。在下
面的语句中，您可能会认为 MySQL 会先评估“@a”，然后再做第二次赋值：
```
SELECT @a, @a:=@a+1, ...;
```
然而，对涉及用户变量的表达式的评估顺序是没有定义的。

另一个问题：将值赋值给一个变量并在同一个非 SET 语句中读取值时，变量的默认结果类
型基于语句开头的类型。下面的例子说明了这一点：
```
mysql> SET @a='test';
mysql> SELECT @a,(@a:=20) FROM tbl_name;
```
对于这个“SELECT”语句，MySQL 向客户端报告第一列是字符串，并将 `@a` 的所有访问转
换成字符串，即使“@a”第二行被设置为数字。在执行“SELECT”语句之后，`@a` 被认为
是下一个语句的数字。

为了避免这种行为的问题，要么不要在同一个语句中为同一个变量分配值后又读取值，要么
将变量设置为 `0`、`0.0` 或者 ''，用来在使用它之前定义它的类型。

在“SELECT”语句中，每个 SELECT 表达式仅在发送给客户机时才进行评估。这意味着在“
HAVING”、“GROUP BY”或“ORDER BY”子句中引用一个在 SELECT 表达式列表中被赋值的
变量并不像预期的那样工作：
```
mysql> SELECT (@aa:=id) AS a, (@aa+3) AS b FROM tbl_name HAVING b=5;
```
“HAVING”子句中对“b”的引用指的是select列表中的一个使用“@aa”的表达式的别名。
这并不像预期的那样工作：“@aa”包含来自前一个选定行的“id”的值，而不是来自当前
行。

用户变量旨在提供数据值。它们不能直接在 SQL 语句中作为标识符或标识符的一部分使用
，例如在需要表名或数据库名的上下文中，或者作为一个保留字如“SELECT”使用，即使该
变量被通过引号引用了也不能，如下面的例子所示：
```
mysql> SELECT c1 FROM t;
+----+
| c1 |
+----+
| 0 |
+----+
| 1 |
+----+
2 rows in set (0.00 sec)

mysql> SET @col = "c1";
Query OK, 0 rows affected (0.00 sec)
mysql> SELECT @col FROM t;
+------+
| @col |
+------+
| c1 |
+------+
1 row in set (0.00 sec)
mysql> SELECT `@col` FROM t;
ERROR 1054 (42S22): Unknown column '@col' in 'field list'

mysql> SET @col = "`c1`";
Query OK, 0 rows affected (0.00 sec)
mysql> SELECT @col FROM t;
+------+
| @col |
+------+
| `c1` |
+------+
1 row in set (0.00 sec)
```

用户变量不能用来提供标识符这个原则的一个例外是：通过构造一个字符串用于预处理语句
以便稍后执行。在这种情况下，可以使用用户变量来组成语句的任何部分。下面的例子说明
了这是如何做到的：
```
mysql> SET @c = "c1";
Query OK, 0 rows affected (0.00 sec)

mysql> SET @s = CONCAT("SELECT ", @c, " FROM t");
Query OK, 0 rows affected (0.00 sec)

mysql> PREPARE stmt FROM @s;
Query OK, 0 rows affected (0.04 sec)
Statement prepared

mysql> EXECUTE stmt;
+----+
| c1 |
+----+
| 0 |
+----+
| 1 |
+----+
2 rows in set (0.00 sec)

mysql> DEALLOCATE PREPARE stmt;
Query OK, 0 rows affected (0.00 sec)
```
更多信息参考：Section 13.5, “Prepared SQL Statement Syntax”

在应用程序中可以使用类似的技术来使用程序变量构造 SQL 语句，如下所示使用 PHP 5：
```
<?php
$mysqli = new mysqli("localhost", "user", "pass", "test");

if( mysqli_connect_errno() )
    die("Connection failed: %s\n", mysqli_connect_error());

$col = "c1";

$query = "SELECT $col FROM t";

$result = $mysqli->query($query);

while($row = $result->fetch_assoc())
{
    echo "<p>" . $row["$col"] . "</p>\n";
}

$result->close();

$mysqli->close();
?>
```
以这种方式组装 SQL 语句有时被称为“动态SQL”。
