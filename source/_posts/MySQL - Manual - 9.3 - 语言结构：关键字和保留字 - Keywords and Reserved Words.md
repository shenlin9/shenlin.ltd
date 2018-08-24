---
title: MySQL - Manual - 9.3 - 语言结构：关键字和保留字 - Keywords and Reserved Words
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 语言结构：关键字和保留字

<!--more-->

关键字是在 SQL 中具有重要意义的词。某些关键字，如 “SELECT”、“DELETE” 或 “
BIGINT”都是保留字，用作表名和列名等标识符时需要特殊的处理。对于内置函数的名称也
可能是这样。

非保留关键字不引用就可以作为标识符。保留的关键字则需要像 Section 9.2, “Schema
Object Names”描述的那样引用它们才可以作为标识符：
```
mysql> CREATE TABLE interval (begin INT, end INT);
ERROR 1064 (42000): You have an error in your SQL syntax ...
near 'interval (begin INT, end INT)'
```

`BEGIN` 和 `END` 是关键字，但不是保留字，所以它们作为标识符的使用不需要引用。
`INTERVAL` 是一个保留的关键字，作为标识符使用则必须被引用：
```
mysql> CREATE TABLE `interval` (begin INT, end INT);
Query OK, 0 rows affected (0.01 sec)
```

例外：在一个限定名内，英文句号后面的单词肯定是一个标识符，因此此单词不需要被引用
，即使是保留字：
```
mysql> CREATE TABLE mydb.interval (begin INT, end INT);
Query OK, 0 rows affected (0.01 sec)
```

内置函数的名称可以作为标识符，但可能需要小心使用。例如，`COUNT` 作为列名是可以接
受的。然而在默认情况下，函数调用时函数名和后面的 `(` 字符之间不允许空格。这个要
求使解析器能够区分名称是在函数调用中还是在非函数上下文中使用。有关函数名称识别的
详细信息，请参见 Section 9.2.4, “Function Name Parsing and Resolution”

`INFORMATION_SCHEMA.KEYWORDS` 表里列出了 MySQL 的关键字以及表明了它们是否是保留
字，参考：Section 24.12, “The INFORMATION_SCHEMA KEYWORDS Table”.

### MySQL 8.0 Keywords and Reserved Words

下面的列表显示了 MySQL 8.0 中的关键字和保留字，以及版本之间个别单词的变化。保留
的关键字被标记为（R）。另外，`_FILENAME` 是保留的。

在某种情况下，你可能会升级到更高版本，所以最好还是先看看未来的保留字吧。你可以在
涵盖了 MySQL 更高版本的手册中找到这些保留字，列表中大多数保留字都被标准 SQL 禁止
作为列名或表名（例如，GROUP）。有一些是保留的，因为 MySQL 需要它们，并使用 yacc
解析器。

    ........
    • TIMESTAMP
    ........
    • VCPU added in 8.0.3 (nonreserved)
    ........
    • WINDOW (R) added in 8.0.2 (reserved)
    • WITH (R)
    • WITHOUT
    ...略...
    ........

### MySQL 8.0 New Keywords and Reserved Words

下面的列表显示了在 MySQL 8.0 中添加的关键字和保留字，与 MySQL 5.7 相比。保留的关
键字用（R）标记。

    ........
    ...略...
    ........

### MySQL 8.0 Removed Keywords and Reserved Words

下面的列表显示了在 MySQL 8.0 中删除的关键字和保留字，与 MySQL 5.7 相比。保留的关
键字用（R）标记。

    • ANALYSE
    • DES_KEY_FILE
    • PARSE_GCOL_EXPR
    • REDOFILE
    • SQL_CACHE
