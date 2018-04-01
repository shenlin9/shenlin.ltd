---
title: MySQL - Manual - 04 - 组件概述
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 安装后包含有许多不同的程序，大多数 MySQL 发行版包含所有这些程序，除了那些
特定于平台的程序（例如，服务器启动脚本在Windows上不被使用）。唯一的例外是 RPM 发
行版更加细分和专业，例如服务器端程序是一个 RPM 包，客户端程序又是一个 RPM 包，等
等。

<!--more-->

## MySQL 服务器端组件

mysqld 是 MySQL 的服务器端主程序，完成了绝大部分的工作，相关的几个脚本帮助启动和
停止 mysqld.

*  mysqld

就是 MySQL 的服务器程序，SQL 的守护进程

See Section 4.3.1, “mysqld —The MySQL Server”.

*  mysqld_safe

是一个服务器启动脚本，会尝试启动 mysqld

See Section 4.3.2, “mysqld_safe —MySQL Server Startup Script”.

*  mysqld_multi

也是一个服务器启动脚本，但可以启动或停止系统上安装的多个服务器程序

See Section 4.3.4,“mysqld_multi — Manage Multiple MySQL Servers”.

*  mysql.server

也是一个服务器启动脚本，且调用了 mysqld_safe，但它是 System-V 风格的（即把脚本放
在特定目录中来为特定的运行级别启动服务）

See Section 4.3.3, “mysql.server — MySQL Server Startup Script”.

## MySQL 安装和更新组件

*  comp_err

在 MySQL 安装过程中从错误源文件编译错误消息文件

See Section 4.4.1, “comp_err— Compile MySQL Error Message File”.

*  mysql_install_db

初始化 MySQL 数据目录，创建 `mysql` 数据库并使用缺省权限初始化 grant 表，并设置
InnoDB 表空间 (sets up the InnoDB system tablespace)。

通常只在 MySQL 安装到系统时执行一次。??? 被自动调用一次

See Section 4.4.2, “mysql_install_db — Initialize MySQL Data
Directory”, and Section 2.10, “Postinstallation Setup and Testing”.

*  mysql_plugin

配置 MySQL 服务器插件

See Section 4.4.3, “mysql_plugin— Configure MySQL Server Plugins”.

*  mysql_secure_installation

提高 MySQL 安装的安全性

See Section 4.4.4,“mysql_secure_installation — Improve MySQL Installation
Security”.

*  mysql_ssl_rsa_setup

创建用于安全连接的 SSL 证书和 RSA 密钥对文件

See Section 4.4.5, “mysql_ssl_rsa_setup — Create SSL/RSA Files”.

*  mysql_tzinfo_to_sql

使用主机系统 zoneinfo 数据库（描述时区的文件集）的内容，在 mysql 数据库中加载时区表

See Section 4.4.6, “mysql_tzinfo_to_sql— Load the Time Zone Tables”.

*  mysql_upgrade

在 MySQL 升级后，检测表不兼容的部分并在必要时尝试修复，按照新版本 MySQL 对授权表
的更改更新授权表

See Section 4.4.7, “mysql_upgrade — Check and Upgrade MySQL Tables”.

## MySQL 客户端组件

* mysql

命令行工具 ：可以交互式地输入SQL语句，或者以批处理模式从文件中执行 SQL 语句

See Section 4.5.1, “mysql — The MySQL Command-Line Tool”.

* mysqladmin

执行管理操作的客户端，例如创建或删除数据库，重载授权表，把表写入磁盘，重新打开日
志文件，还可以从服务器检索版本，进程和状态信息

See Section 4.5.2, “mysqladmin — Client for Administering a MySQL Server”.

* mysqlcheck

负责维护表的客户端，包括检查、修复、分析和优化。

See Section 4.5.3,“mysqlcheck — A Table Maintenance Program”.

* mysqldump

将 MySQL 数据库转储为 SQL 文件、文本文件或 XML 文件的客户端

See Section 4.5.4, “mysqldump— A Database Backup Program”.

* mysqlimport

使用`LOAD DATA INFILE`语法把文本文件的内容导入到各自的表中的客户端

See Section 4.5.5,“mysqlimport — A Data Import Program”.

* mysqlpump

将 MySQL 数据库转储为 SQL 文件的客户端

See Section 4.5.6, “mysqlpump — A Database Backup Program”.

* mysqlsh

MySQL Shell，是一个高级的命令行客户端和代码编辑器，除了 SQL 语句，还兼容
JavaScript 和 Python 脚本，MySQL Shell 通过 X 协议连接到 MySQL 服务器，并使用X
DevAPI来处理关系型数据和文档数据

See Section 4.5.7, “mysqlsh — The MySQL Shell”.

* mysqlshow

显示关于数据库、表、列和索引的信息的客户端

See Section 4.5.8,“mysqlshow — Display Database, Table, and Column
Information”.

* mysqlslap

用来模拟 MySQL 服务器的客户端负载，并报告每个阶段的时间。它的工作方式就像多个客户
端在访问服务器一样

See Section 4.5.9, “mysqlslap — Load Emulation Client”.

## MySQL 管理和实用组件

* innochecksum

一个离线的 InnoDB 离线文件"校验和"实用程序

See Section 4.6.1, “innochecksum — Offline InnoDB File Checksum Utility”.

* myisam_ftdump

显示 MyISAM 表里全文索引信息的实用程序

See Section 4.6.2,“myisam_ftdump — Display Full-Text Index information”.

* myisamchk

用于描述、检查、优化和修复MyISAM表的实用程序

See Section 4.6.3, “myisamchk —MyISAM Table-Maintenance Utility”.

* myisamlog

一个处理 MyISAM 日志文件内容的实用程序

See Section 4.6.4,“myisamlog — Display MyISAM Log File Contents”.

* myisampack

压缩 MyISAM 表以生成更小的只读表的实用程序

See Section 4.6.5,“myisampack — Generate Compressed, Read-Only MyISAM Tables”.

* mysql_config_editor

一个实用程序，它允许您将身份验证凭证存储在一个安全的、加密的登录路径文件中，名为
.mylogin.cnf

See Section 4.6.6, “mysql_config_editor — MySQL Configuration Utility”.

* mysqlbinlog

一个用于从二进制日志中读取 SQL 语句的实用程序。二进制日志文件中包含的执行语句可
以用来帮助从崩溃中恢复

See Section 4.6.7, “mysqlbinlog — Utility for Processing Binary Log Files”.

* mysqldumpslow

用于读取和总结慢速查询日志的实用程序

See Section 4.6.8, “mysqldumpslow— Summarize Slow Query Log Files”.

## MySQL 程序开发工具

MySQL program-development utilities:

* mysql_config

一个在编译 MySQL 程序时生成所需选项值的 shell 脚本

See Section 4.7.1, “mysql_config — Display Options for Compiling Clients”.

* my_print_defaults

一个实用程序，它显示选项文件的选项组中有哪些选项

See Section 4.7.2,“my_print_defaults — Display Options from Option Files”.

* resolve_stack_dump

一个实用程序，它将一个数值堆栈跟踪转储到符号
A utility program that resolves a numeric stack trace dump to symbols.

See Section 4.7.3,“resolve_stack_dump — Resolve Numeric Stack Trace Dump to
Symbols”.

## 其他各式各样的工具

Miscellaneous utilities:

* lz4_decompress

一个实用程序，可以解压“mysqlpump”输出，该输出是使用 LZ4 压缩创建的

See Section 4.8.1, “lz4_decompress — Decompress mysqlpump
LZ4-Compressed Output”.

* perror

一个显示系统或MySQL错误码含义的实用程序

See Section 4.8.2, “perror —Explain Error Codes”.

* replace

一个在输入文本中执行字符串替换的实用程序程序

See Section 4.8.3, “replace — A String-Replacement Utility”.

* resolveip

一个实用程序，它将主机名解析为IP地址，反之亦然

See Section 4.8.4,“resolveip — Resolve Host name to IP Address or Vice Versa”.

* zlib_decompress

一个实用程序，可以解压“mysqlpump”输出，该输出是使用 ZLIB 压缩创建的

See Section 4.8.5, “zlib_decompress — Decompress mysqlpump
ZLIB-Compressed Output”.

* Oracle 还提供了 MySQL Workbench GUI tool，用于管理 MySQL 服务器和数据库，用于
  创建、执行和评估查询，以及从其他关系数据库管理系统中迁移数据库和数据以供MySQL
  使用

* 额外的 GUI 工具包括 MySQL Notifier 和 MySQL for Excel

## Temp

与 MySQL 服务器通信的 MySQL 客户端程序使用以下环境变量

|环境变量|含义|
|---|---|
|MYSQL_UNIX_PORT|默认的 Unix 套接字文件，用于连接到 localhost|
|MYSQL_TCP_PORT|默认端口号，用于 TCP/IP 连接|
|MYSQL_PWD|默认密码|
|MYSQL_DEBUG|调试的时候调试跟踪选项|
|TMPDIR|创建临时表和文件的目录|

对于 MySQL 程序所使用的环境变量的完整列表
see Section 4.9, “MySQL Program Environment Variables”.

使用 MYSQL_PWD 是不安全的
See Section 6.1.2.1, “End-User Guidelines for Password Security”.

## 选项

### 通用选项

一些儿选项是所有 MySQL 客户端程序都支持的，如：

-h 或 --host
-u 或 --user
-p 或 --password
-P 或 --port        TCP/IP 端口号 
-S 或 --socket      套接字文件的路径

### 非选项参数

非选项参数即参数前不带 `-` 或 `--` 的参数

mysql 客户端程序的第一个非选项参数是要连接的数据库名

### 选项默认值

一些儿选项具有默认值，当不指定选项值时则使用默认值

-h 默认值 localhost
-u Windows 下默认值 ODBC，Linux 下为当前登录的用户名
-p 不提供此选项则不发送密码

例如
```
$ mysql
```
不带任何参数，则使用默认值，表示连接 localhost 主机，用户名是 ODBC 或 当前登录用
户，不发送密码，没有指定要连接的数据库

或者指定参数
```
shell> mysql --host=localhost --user=myname --password mydb
shell> mysql -h localhost -u myname -p mydb
```

## 

MySQL 是双重许可，用户既可以根据 GPL 的条款把 MySQL 软件作为开源产品，或者可以从
Oracle 购买标准的商业许可证。
