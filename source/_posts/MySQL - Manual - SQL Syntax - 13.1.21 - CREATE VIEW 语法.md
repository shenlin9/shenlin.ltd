---
title: MySQL - Manual - SQL Syntax - 13.1.21 - CREATE VIEW 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - CREATE VIEW
---

MySQL `CREATE VIEW` 语法

<!--more-->

```
CREATE
    [OR REPLACE]
    [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    [DEFINER = { user | CURRENT_USER }]
    [SQL SECURITY { DEFINER | INVOKER }]
VIEW view_name [(column_list)]
AS select_statement
    [WITH [CASCADED | LOCAL] CHECK OPTION]
```

`CREATE VIEW` 语句创建一个新视图，或者在给出 `OR REPLACE` 子句时替换现有视图。如
果视图不存在，`CREATE OR REPLACE VIEW` 与 `CREATE VIEW` 相同。如果视图确实存在，
`CREATE OR REPLACE VIEW` 将替换它。

有关使用视图的限制，参考 Section C.5, “Restrictions on Views”.

`select_statement` 是提供视图定义的 `SELECT` 语句。(从视图中选择实际上是使用
`SELECT` 语句进行选择。) `select_statement` 可以从基本表或其他视图中进行选择。

视图定义在创建时就被“冻结”了，这之后不受底层表定义的后续更改的影响。例如，如果
一个视图在表上定义为 `SELECT *`，那么以后添加到表中的新列不会成为视图的一部分，
并且从表中删除列后，再从视图中进行选择时将导致错误。

`ALGORITHM` 子句影响 MySQL 处理视图的方式。
`DEFINER` 和 `SQL SECURITY` 子句指定在视图调用阶段检查访问特权时使用的安全上下文。
`WITH CHECK OPTION` 子句可用于约束视图引用的表中行的插入或更新。
本节稍后将描述这些子句。

`CREATE VIEW` 语句需要视图的 `CREATE VIEW` 特权，以及由 `SELECT` 语句选择的每个
列的某些特权。对于 `SELECT` 语句中其他地方使用的列，必须具有 `SELECT` 特权。如果
存在 `OR REPLACE` 子句，则还必须具有视图的 `DROP` 特权。`CREATE VIEW` 可能还需要
`SET_USER_ID` 或 `SUPER` 特权，这取决于 `DEFINER` 值，如本节后面所述。

引用视图时，将按照本节后面的描述进行特权检查。

一个视图属于一个数据库。默认情况下，新视图被创建在默认数据库中。要在指定数据库中
显式的创建视图，请使用 `db_name.view_name` 语法用数据库名限定视图名：
```
CREATE VIEW test.v AS SELECT * FROM t;
```

`SELECT` 语句中的未限定表名或视图名被解释为默认数据库。视图可以通过使用适当的数
据库名称限定表或视图名称来引用其他数据库中的表或视图。

在数据库中，基本表和视图共享相同的名称空间，因此基本表和视图不能具有相同的名称。

`SELECT` 语句检索的列可以是对表列的简单引用，也可以是对使用函数、常量值、操作符
等的表达式的引用。

视图必须具有惟一的列名，没有重复，就像基本表一样。默认情况下， `SELECT` 语句检索
到的列名用于视图列名。要为视图列明确定义名称，请将可选的 `column_list` 子句指定
为用逗号分隔的标识符列表。 `column_list` 中的名称数量必须与 `SELECT` 语句检索到
的列数量相同。

可以从许多种 `SELECT` 语句创建视图。它可以引用基本表或其他视图。它可以使用连接、
`UNION` 和子查询。`SELECT` 甚至不需要引用任何表：
```
CREATE VIEW v_today (today) AS SELECT CURRENT_DATE;
```

下面的例子定义了一个视图，该视图从另一个表中选择了两列和这些列的计算表达式:
```
mysql> CREATE TABLE t (qty INT, price INT);
mysql> INSERT INTO t VALUES(3, 50);

mysql> CREATE VIEW v AS SELECT qty, price, qty*price AS value FROM t;
mysql> SELECT * FROM v;
+------+-------+-------+
| qty | price | value |
+------+-------+-------+
| 3   | 50    | 150   |
+------+-------+-------+
```

视图定义受以下限制：
* `SELECT` 语句不能引用系统变量或用户定义变量。
* 在存储程序中，`SELECT` 语句不能引用程序参数或局部变量。
* `SELECT` 语句不能引用预处理语句的参数。
* 定义中引用的任何表或视图都必须存在。如果在创建视图之后，删除定义引用的表或视图
  ，则使用视图将导致错误。要检查有此类问题的视图定义，请使用 `CHECK TABLE` 语句
  。
* 视图定义不能引用 `TEMPORARY` 表，也不能创建一个 `TEMPORARY` 视图。
* 不能把视图和触发器关联。
* `SELECT` 语句中列别名根据最大列长度 64 个字符(而不是最大别名长度 256 个字符)进
  行检查。

`ORDER BY` 在视图定义中是允许的，但是如果您从视图中选择时使用的语句本身就有
`ORDER BY`，它将被忽略。

对于视图定义中的其他选项或子句，它们会被添加到引用视图的语句的选项或子句中，但是
效果是未定义的。例如，如果一个视图定义包含一个 `LIMIT` 子句，而您从视图中进行选
择时使用的语句也带有自己的 `LIMIT` 子句，那么应用哪个 `LIMIT` 是未定义的。这一原
则同样适用于 `ALL`、`DISTINCT` 或 `SQL_SMALL_RESULT` 跟在 `SELECT` 关键字后面
的选项，也同样适用于 `INTO`、`FOR UPDATE`、`FOR SHARE`、`LOCK IN SHARE MODE` 和
`PROCEDURE` 等子句。

如果您通过更改系统变量来更改查询处理环境，则从视图中获得的结果可能会受到影响：
```
mysql> CREATE VIEW v (mycol) AS SELECT 'abc';
Query OK, 0 rows affected (0.01 sec)

mysql> SET sql_mode = '';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT "mycol" FROM v;
+-------+
| mycol |
+-------+
| mycol |
+-------+
1 row in set (0.01 sec)

mysql> SET sql_mode = 'ANSI_QUOTES';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT "mycol" FROM v;
+-------+
| mycol |
+-------+
| abc |
+-------+
1 row in set (0.00 sec)
```

`DEFINER` 和 `SQL SECURITY` 子句决定了在执行引用了视图的语句时检查视图的访问权限
时使用哪个 MySQL 帐户。有效的 `SQL SECURITY` 特征值是 `DEFINER`(默认值)和
`INVOKER`，分别表明所需的特权必须由定义视图或调用视图的用户持有。

如果为 `DEFINER` 子句指定了一个用户值，那么它应该是一个 MySQL 帐户，指定为
`'user_name'@'host_name'`、`CURRENT_USER` 或 `CURRENT_USER()`。`DEFINER` 的默认
值是执行 `CREATE VIEW` 语句的用户。这与显式指定 `DEFINER = CURRENT_USER` 相同。

如果有 `DEFINER` 子句，则这些规则确定有效的 `DEFINER` 用户值：
* 如果您没有 `SET_USER_ID` 或 `SUPER` 特权，唯一有效的用户值是您自己的帐户，可以
  使用字面量指定，也可以使用 `CURRENT_USER` 指定。您不能将 `definer` 设置为其他
  帐户。
* 如果您拥有 `SET_USER_ID` 或 `SUPER` 特权，您可以指定任何语法上有效的帐户名称。
  如果该帐户不存在，将生成警告。
* 虽然创建一个不存在的 `DEFINER` 帐户的视图是可能的，但是如果 `SQL SECURITY` 值
  是 `DEFINER`，但是 `DEFINER` 帐户不存在，那么在引用视图时就会发生错误。

更多关于视图安全性的信息，参考 Section 23.6, “Access Control for Stored
Programs and Views”.

在视图定义中，`CURRENT_USER` 默认返回视图的 `DEFINER` 值。对于使用 `SQL SECURITY
INVOKER` 特性定义的视图，`CURRENT_USER` 返回视图调用程序的帐户。有关视图中用户审
计的信息，请参见 Section 6.3.13, “SQL-Based MySQL Account Activity Auditing”。

在使用 `SQL SECURITY DEFINER` 特征定义的存储例程中，`CURRENT_USER` 返回例程的
`DEFINER` 值，这也会影响在这样一个例程中定义的视图，如果视图定义包含 `DEFINER`
值 `CURRENT_USER`。

MySQL 按如下检查视图权限：
* 在视图定义时，视图创建者必须具有使用视图访问的顶级对象所需的特权。例如，如果视
  图定义引用表列，则创建者必须对定义中的选择列表中的每一列具有某些特权，对定义中
  其他地方使用的每一列具有 `SELECT` 特权。如果视图定义引用存储函数，则只能检查调
  用该函数所需的特权。函数调用时所需的特权只能在执行时检查：对于不同的调用，可能
  会采用函数内的不同执行路径。
* 引用视图的用户必须具有访问视图的适当权限(从视图中选择的 `SELECT` 特权，插入视
  图的 `INSERT` 特权，等等)。
* 当一个视图被引用时，被视图访问的对象的特权将与视图 `DEFINER` 帐户或调用程序所
  拥有的特权进行对比，这取决于 `SQL SECURITY` 特征是 `DEFINER` 还是 `INVOKER`。
* 如果对视图的引用导致了存储函数的执行，那么在函数中执行的语句的权限检查取决于函
  数 `SQL SECURITY` 特征是 `DEFINER` 还是 `INVOKER` 。如果安全特性是 `DEFINER`
  ，则该函数以 `DEFINER` 帐户的特权运行。如果特征是 `INVOKER` ，则该函数以视图的
  `SQL SECURITY` 特征所决定的特权运行。

示例:视图可能依赖于存储函数，而该函数可能调用其他存储例程。

例如，以下视图调用一个存储函数 `f()`:
```
CREATE VIEW v AS SELECT * FROM t WHERE t.id = f(t.name);
```

假设 `f()` 包含这样一条语句：
```
IF name IS NULL then
    CALL p1();
ELSE
    CALL p2();
END IF;
```

在执行 `f()` 时，需要检查执行 `f()` 中语句所需的特权。这可能意味着 `p1()` 或
`p2()` 需要特权，这取决于 `f()` 中的执行路径。这些特权必须在运行时检查，必须拥有
这些特权的用户由视图 `v` 的 `SQL SECURITY` 值和函数 `f()` 确定。

视图的 `DEFINER` 和 `SQL SECURITY` 子句是对标准 SQL 的扩展。在标准 SQL 中，视图
使用 `SQL SECURITY DEFINER` 的规则来处理。该标准规定，视图的定义者(与视图模式的
所有者相同)可以获得视图上的适用特权(例如，`SELECT`)，并可以授予这些特权。MySQL
没有模式“所有者 owner”的概念，所以 MySQL 添加了一个子句来标识定义者。`DEFINER`
子句是一个扩展，其目的是拥有标准所拥有的东西;也就是说，是一个谁定义了视图的永久
记录。这就是为什么 `DEFINER` 的默认值是视图创建者的帐户。

可选的 `ALGORITHM` 子句是标准 SQL 的 MySQL 扩展。它影响 MySQL 处理视图的方式。
`ALGORITHM` 有三个值：`MERGE`、`TEMPTABLE` 或 `UNDEFINED`。有关更多信息，请参见
Section 8.2.2.3, “Optimizing Derived Tables, View References, and Common Table
Expressions”。

有些视图是可更新的。也就是说，您可以在 `UPDATE`、`DELETE` 或 `INSERT` 等语句中使
用视图来更新底层表的内容。要更新视图，视图中的行与底层表中的行之间必须存在一对一
的关系。还有一些其他结构使视图不可更新。

视图中生成的列被认为是可更新的，因为可以对其进行赋值。但是，如果这样的列被显式更
新，那么唯一允许的值就是 `DEFAULT`。有关生成的列的信息，请参见 Section
13.1.18.8, “CREATE TABLE and Generated Columns”

可以为可更新视图提供 `WITH CHECK OPTION` 子句，以防止对行进行插入或更新，但
`select_statement` 中的 `WHERE` 子句为真的行除外。

在可更新视图的 `WITH CHECK OPTION` 子句中，若当前视图是根据另一个视图定义的，
`LOCAL` 和 `CASCADE` 关键字确定了对照测验的范围。`LOCAL` 关键字将 `CHECK OPTION`
限制在被定义的视图中。`CASCADE` 还会对底层视图的检查进行评估。当两个关键字都没有
给出时，默认是 `CASCADE`。

有关可更新视图和 `WITH CHECK OPTION` 子句的更多信息，请参见 Section 23.5.3,“
Updatable and Insertable Views”, Section 23.5.4, “The View WITH CHECK OPTION
Clause”



