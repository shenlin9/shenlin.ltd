---
title: MySQL - Manual - 10.3 - 字符集：指定字符集和校对规则 - Specifying Character Sets and Collations
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 字符集：MySQL 字符集和校对规则

<!--more-->

在四个层级上有字符集和校对规则的默认设置：服务器、数据库、表和列。下面几节中的描
述可能看起来很复杂，但在实践中发现，多级默认会导致自然和明显的结果。

`CHARACTER SET` 用于在子句中指定字符集，`CHARSET` 可以作为 `CHARACTER SET` 的同
义词使用。

字符集问题不仅影响数据存储，还影响客户端程序和 MySQL 服务器之间的通信。如果您希
望客户端程序使用与缺省字符集不同的值来与服务器通信，那么您需要指出是哪一个字符集
。例如，要使用 `utf8mb4` Unicode 字符集，在连接到服务器后执行此语句：
```
SET NAMES 'utf8mb4';
```

有关在客户端/服务器通信中与字符集设置相关问题的更多信息，参考：Section 10.4,“
Connection Character Sets and Collations”.

## 10.3.1 Collation Naming Conventions 



## 10.3.2 Server Character Set and Collation 



## 10.3.3 Database Character Set and Collati



## 10.3.4 Table Character Set and Collation



## 10.3.5 Column Character Set and Collation



## 10.3.6 Character String Literal Character Set and Collation



## 10.3.7 The National Character Set



## 10.3.8 Character Set Introducers



## 10.3.9 Examples of Character Set and Collation Assignment 



## 10.3.10 Compatibility with Other DBMSs
