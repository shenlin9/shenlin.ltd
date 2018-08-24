---
title: MySQL - Manual - 9.5 - 语言结构：表达式语法 - Expression Syntax
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 语言结构：表达式语法

<!--more-->

下面的规则定义了 MySQL 中的表达式语法。这里显示的语法是基于 MySQL 源代码发行版中
的文件 `sql/sql_yacc.yy` 给出的。请参阅语法后的注释，以获得关于某些术语的附加信
息。

```
expr:
    expr OR expr
    | expr || expr
    | expr XOR expr
    | expr AND expr
    | expr && expr
    | NOT expr
    | ! expr
    | boolean_primary IS [NOT] {TRUE | FALSE | UNKNOWN}
    | boolean_primary

boolean_primary:
    boolean_primary IS [NOT] NULL
    | boolean_primary <=> predicate
    | boolean_primary comparison_operator predicate
    | boolean_primary comparison_operator {ALL | ANY} (subquery)
    | predicate

comparison_operator: = | >= | > | <= | < | <> | !=

predicate:
    bit_expr [NOT] IN (subquery)
    | bit_expr [NOT] IN (expr [, expr] ...)
    | bit_expr [NOT] BETWEEN bit_expr AND predicate
    | bit_expr SOUNDS LIKE bit_expr
    | bit_expr [NOT] LIKE simple_expr [ESCAPE simple_expr]
    | bit_expr [NOT] REGEXP bit_expr
    | bit_expr

bit_expr:
    bit_expr | bit_expr
    | bit_expr & bit_expr
    | bit_expr << bit_expr
    | bit_expr >> bit_expr
    | bit_expr + bit_expr
    | bit_expr - bit_expr
    | bit_expr * bit_expr
    | bit_expr / bit_expr
    | bit_expr DIV bit_expr
    | bit_expr MOD bit_expr
    | bit_expr % bit_expr
    | bit_expr ^ bit_expr
    | bit_expr + interval_expr
    | bit_expr - interval_expr
    | simple_expr

simple_expr:
    literal
    | identifier
    | function_call
    | simple_expr COLLATE collation_name
    | param_marker
    | variable
    | simple_expr || simple_expr
    | + simple_expr
    | - simple_expr
    | ~ simple_expr
    | ! simple_expr
    | BINARY simple_expr
    | (expr [, expr] ...)
    | ROW (expr, expr [, expr] ...)
    | (subquery)
    | EXISTS (subquery)
    | {identifier expr}
    | match_expr
    | case_expr
    | interval_expr
```

操作符优先级，参考 Section 12.3.1, “Operator Precedence”
字面值语法，参考 Section 9.1, “Literal Values”
标识符语法，参考 Section 9.2, “Schema Object Names”

变量可以是用户变量、系统变量或存储程序局部变量或参数：
* 用户变量: Section 9.4, “User-Defined Variables”
* 系统变量: Section 5.1.8, “Using System Variables”
* 本地变量: Section 13.6.4.1, “Local Variable DECLARE Syntax”
* 参数: Section 13.1.15, “CREATE PROCEDURE and CREATE FUNCTION Syntax”

`param_marker` 就是 `?`，用于预处理语句中的占位符，参考 Section 13.5.1, “
PREPARE Syntax”.

`(subquery)` 表示返回单个值的子查询，也就是返回一个标量的子查询，参考 Section
13.2.11.1, “The Subquery as Scalar Operand”.

`{identifier expr}` is ODBC escape syntax and is accepted for ODBC compatibility. The value is expr. The curly braces in the syntax should be written literally; they are not metasyntax as used elsewhere in syntax descriptions.
`{identifier expr}` 是 ODBC 转义语法，并为了 ODBC 兼容性而接受。值是 `expr`。语
法中的花括号应该就按字面书写，它们不是在语法描述中使用的元数据。

`match_expr` 表明了一个 `MATCH` 表达式. 参考 Section 12.9, “Full-Text Search Functions”.

`case_expr` 表明了一个 `CASE` 表达式. 参考 Section 12.4, “Control Flow Functions”.

`interval_expr` 代表一个时间间隔，语法是 `INTERVAL expr unit`, 其中 `unit` 是说
明符（specifier）例如 `HOUR`, `DAY`, `WEEK` 等，完整列表请查询函数 `DATE_ADD()`
描述，位于 Section 12.7, “Date and Time Functions”.

操作符的含义取决于 SQL 模式：

* 默认 `||` 是个逻辑 `OR` 操作符，启用 `PIPES_AS_CONCAT` 后，`||` 是字符串连接符
  ，优先级位于 `^` 和一元操作符之间。
* 默认 `!` 的优先级高于 `NOT`，启用 `HIGH_NOT_PRECEDENCE` 后，两者优先级相同。

参考： Section 5.1.10, “Server SQL Modes”.
