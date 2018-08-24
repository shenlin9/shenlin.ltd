---
title: MySQL - Manual - 10.0 - 字符集 - Character Sets
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 字符集概述

<!--more-->

MySQL 包括的字符集支持：
* 允许您使用各种各样的字符集存储数据，并根据各种校对规则进行比较。
* 可以在服务器、数据库、表和列各种级别上指定字符集。
* MySQL 支持在 MyISAM、MEMORY 和 InnoDB 存储引擎上使用字符集。

本章讨论以下主题：
* 什么是字符集和校对规则？
* 用于字符集分配的多级默认系统。
* 用于指定字符集和校对规则的语法。
* 受影响的函数和操作。
* Unicode 支持。
* 可用的字符集和排序规则，以及注释。
* 为错误信息选择语言。
* 为月、日的名字选择语言区域。

字符集问题不仅影响数据存储，还影响客户端程序和 MySQL 服务器之间的通信。如果您希
望客户端程序使用与缺省值不同的字符集来与服务器通信，那么您需要指出是哪一个。例如
，要使用 utf8 Unicode 字符集，在连接到服务器后执行以下语句：
```
SET NAMES 'utf8';
```

有关在客户端/服务器通信中应用程序的字符集配置和相关问题的更多信息参考：Section
10.5, “Configuring Application Character Set and Collation”, Section 10.4, “
Connection Character Sets and Collations”.
