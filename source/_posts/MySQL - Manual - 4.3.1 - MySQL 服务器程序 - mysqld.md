---
title: MySQL - Manual - 4.3.1 - MySQL 服务器程序 - mysqld
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

mysqld

<!--more-->

mysqld 也被称为 MySQL Server，负责管理包含了数据库和表的 MySQL 数据目录的存取，
数据目录也是其他信息的默认目录如日志文件和状态文件。

mysqld 启动后开始监听客户端的网络连接并代表客户端管理对数据库的访问。

mysqld 有很多选项，查看选项的完整列表：
```bash
shell> mysqld --verbose --help
```

mysqld 也有一组系统变量，这些变量会影响它运行时的操作，系统变量可以在服务器启动
时设置，许多也可以在运行时更改，以影响动态服务器重新配置

MySQL服务器也有一组状态变量，提供了关于其操作的信息。您可以监视这些状态变量以获
取运行时性能特征

对于 MySQL Server command options, system variables, status variables 的完整描述
参见 Section 5.1, “The MySQL Server”.

对于安装 MySQL 和建立初始化设置参见 Chapter 2, Installing and Upgrading MySQL.
