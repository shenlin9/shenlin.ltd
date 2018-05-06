---
title: MySQL - 01 - 概述
categories: 
 - MySQL
tags: 
 - MySQL
---

MySQL 概述

<!--more-->

## 提示符说明

提示符指明了命令的执行环境
```
shell> type a shell command here
root-shell> type a shell command as root here
mysql> type a mysql statement here
```
* 上面的 shell 为默认的 bash，其他不同的 shell 语法可能不同

提示符还可以指明分布式环境
```
master> type a mysql command on the replication master here
slave> type a mysql command on the replication slave here
```

## 文档语法说明

`[]` 用于表示可选项
```mysql
DROP TABLE [IF EXISTS] tbl_name
```

`|` 用于分隔并列的多项
```mysql
TRIM([[BOTH | LEADING | TRAILING] [remstr] FROM] str)
```

`{}` 用于表示必选项
```mysql
{DESCRIBE | DESC} tbl_name [col_name | wild]
```

`...` 用于表示项可以多个重复
```mysql
RESET reset_option [,reset_option] ...
```

## 关系数据库

关系数据库将数据存储在不同的表中，而不是将所有数据存放在一个大存储库中。

数据库结构被组织成为速度而优化的物理文件。带有数据库、表格、视图、行和列等对象的
逻辑模型提供了灵活的编程环境。

用户建立管理不同数据字段之间关系的规则，例如1-1、一对多、惟一、必需或可选，以及
不同表之间的“指针”。数据库强制执行这些规则，因此，在一个设计良好的数据库中，应
用程序永远不会看到不一致的、重复的、孤儿的、过期的或丢失的数据。

## SQL

Structured Query Language

SQL 标准由 ANSI/ISO 发布，已改进过多次：
1. SQL-92      1992年发布的标准
2. SQL:1999    1999年发布的标准
3. SQL:2003    2003年发布的标准，当前标准，一般提到 SQL 标准即此标准

根据编程环境，SQL 一般有以下3种应用方法：
1. 直接调用 SQL 语言
2. 在其他编程语言中嵌入 SQL
3. 通过其他编程语言提供的 API 隐藏调用 SQL 语法

## MySQL 特点

* MySQL 采用 GPL 和商业双重许可，若需要在商业软件中嵌入 SQL 代码，则需购买商业许可

    GPL 许可： http://www.fsf.org/licenses/
    商业许可： http://www.mysql.com/company/legal/licensing/

* MySQL 以企业版的形式提供技术支持

* MySQL 既可以作为一个客户端/服务器系统，也可以作为嵌入式多线程库，您可以将其链接
到您的应用程序

* “MySQL” 的官方发音是 “My Ess Que Ell”不是 “my sequel”

* MySQL 是以联合创始人 Monty Widenius 的女儿的名字命名的 

* MySQL 标志里的海豚叫 Sakila ，是在“给海豚起名”比赛时从用户推荐的一大堆名字中选出来的

* 使用 C 和 C++ 编写

* 为了方便移植，MySQL 5.5 以及以后版本使用 CMake，以前的版本使用 GNU Automake,
  Autoconf, Libtool

* 通过了 Purify，Valgrind 内存泄漏测试

* 设计为支持多核多线程

* Uses very fast B-tree disk tables (MyISAM) with index compression
* 使用索引压缩 B-tree磁盘表 (MyISAM)

* Designed to make it relatively easy to add other storage engines. This is useful if you want to provide an SQL interface for an in-house database.
* 添加其他存储引擎相对容易，

* Uses a very fast thread-based memory allocation system.
* 使用非常快的基于线程的内存分配系统

* Executes very fast joins using an optimized nested-loop join.
* 使用一个优化的嵌套循环联接来非常快的执行连接

* Implements in-memory hash tables, which are used as temporary tables.
* 临时表使用内存哈希表来实现

* Implements SQL functions using a highly optimized class library that should be as fast as possible.  Usually there is no memory allocation at all after query initialization.
* 使用高度优化的类库实现了 SQL 函数，通常在查询初始化后，就根本不再分配内存

* Provides the server as a separate program for use in a client/server networked environment, and as a library that can be embedded (linked) into standalone applications. Such applications can be used in isolation or in environments where no network is available.
* 在 client/server 网络环境下，把 server 端作为一个单独的程序；在独立应用程序环
    境下，把 server 端作为一个库嵌入/链接到程序，这样的程序可以独立使用和在没有网络的
    环境下使用

## MySQL 语句特性

* 函数名独立于表名，列名，所以 ABS 也可以作为一个有效的列名

* 每个表最多支持 64 个索引，每个索引支持 1 到 16 列

* InnoDB 表的最大索引宽度是 767 bytes 或 3072 bytes，MyISAM 表的最大索引宽度是
    1000 bytes

* 索引可以使用这些列类型作为前缀：CHAR,VARCHAR,TEXT,BLOB

## MySQL 连接

MySQL 客户端连接到服务端可以通过多种协议 ：

1. 任何平台都可以通过 TCP/IP 套接字连接

2. Unix 平台可以通过 Unix 域套接字文件连接

3. 在 Windows 下如果服务端启动时使用了选项 `--enable-named-pipe` 则可以通过命名
   管道连接

4. 在 Windows 下如果服务端启动时使用了选项 `--shared-memory` 则可以通过共享内存
   连接，客户端连接时则要对应的使用选项 `--protocal=memory`

MySQL 客户端程序可以用多种语言编写。用 C 语言编写的客户端库可被用 C 或c++编写的
客户机使用，也可被提供C绑定的任何语言使用。

为下列语言提供了客户端 API : C, C++, Eiffel, Java, Perl, PHP, Python, Ruby, Tcl

• The Connector/ODBC (MyODBC) interface provides MySQL support for client programs that use ODBC (Open Database Connectivity) connections. For example, you can use MS Access to connect to your MySQL server. Clients can be run on Windows or Unix. Connector/ODBC source is available. All ODBC 2.5 functions are supported, as are many others. 

Connector/ODBC (MyODBC) 接口支持使用 ODBC(Open Database Connectivity) 连接的客户端程序，如微软的 Access

• The Connector/J interface provides MySQL support for Java client programs that use JDBC connections.  Clients can be run on Windows or Unix. Connector/J source is available. 

Connector/J 接口支持 Java 客户端程序使用 JDBC 连接 MySQL，客户端可以允许

• MySQL Connector/Net enables developers to easily create .NET applications that require secure, high-performance data connectivity with MySQL. It implements the required ADO.NET interfaces and integrates into ADO.NET aware tools. Developers can build applications using their choice of .NET languages. MySQL Connector/Net is a fully managed ADO.NET driver written in 100% pure C#. 

使用 Connector/Net 接口很容易开发安全、高性能的连接到 MySQL 的 .NET 应用

## 本地化

MySQL 服务端可以为客户端提供多种语言的错误消息

支持多种字符集，在编译和运行时可以指定字符集

所有数据保存时都使用指定的字符集

排序和比较使用默认的字符集和校对，可以在服务端启动时更改此设置

服务端的时区可以动态修改，每个客户端可以指定自己的时区

## 出错后如何报告问题

还未整理，但必须整理....
???

## SQL 模式

MySQL 服务端执行时有多种 SQL 模式可供选择，并可以根据相应的客户端应用不同的模式，

可以为整个服务器设置一个全局 SQL 模式，每个应用又可以设置自己的会话 SQL 模式

SQL 模式使得 MySQL 可以更容易的在不同的环境中使用，可以更好的和其他数据库一起使
用

SQL 模式会影响 MySQL 支持的 SQL　语法和执行的数据验证检查

SQL 模式可以通过系统变量 `sql_mode` 来设定 

###  ANSI Mode

**选择 ANSI Mode**

如果 mysqld 没启动，则启动 `mysqld` 时加选项 `--ansi`
```mysql
--ansi

# 或者这样，效果一样

--transaction-isolation=SERIALIZABLE --sql-mode=ANSI
```

如果 mysqld 已启动，则使用下面命令
```mysql
SET GLOBAL TRANSACTION ISOLATION LEVEL SERIALIZABLE;
SET GLOBAL sql_mode = 'ANSI';
```

注意：

`SET GLOBAL sql_mode='ANSI';` 和 `--ansi` 不完全一样，因为 `--ansi` 还设置了事务
隔离级别

**查看 ANSI Mode**

将 `sql_mode` 系统变量设置为 `ANSI` 后，就自动启用了 SQL 模式中所有和 ANSI 相关
的选项，如下所示
```mysql
mysql> SET GLOBAL sql_mode='ANSI';

mysql> SELECT @@global.sql_mode;
    -> 'REAL_AS_FLOAT,PIPES_AS_CONCAT,ANSI_QUOTES,IGNORE_SPACE,ANSI'
```

## MySQL 对标准 SQL 的扩展

如果使用了 MySQL 的扩展，则代码迁移到其他 SQL 服务器会出问题，但 MySQL 提供了一
种注释方式来表明此处的代码为 MySQL 扩展，则 MySQL 服务器会执行其中的代码，而其他
SQL 服务器则视为注释不予执行，这种情况下代码仍可以迁移，格式:

    /*![version number] MySQL 扩展代码 */

version number 为 MySQL 版本号可省略，表示等于或大于此版本号可使用此特性

例如:

    SELECT /*! STRAIGHT_JOIN */ col1 FROM table1,table2 WHERE ...

    CREATE TABLE t1(a INT, KEY (a)) /*!50110 KEY_BLOCK_SIZE=1024 */;

第二条语句里的 50110 表示 MySQL 5.1.10 以后的版本才执行

## MySQL 对标准 SQL 的扩展

还未整理，但必须整理....
1.8.1
???

## MySQL 和标准 SQL 的不同

尽管 MySQL 遵循 ANSI SQL 标准和 ODBC SQL 标准，但还有一些儿不同

### 查询 

MySQL 不支持 Sybase 公司的 SQL 扩展 `SELECT ... INTO TABLE` 语句，但支持标准 SQL
的 `INSERT INTO ... SELECT` ，例如：
```mysql
INSERT INTO tbl_temp2 (fld_id)
SELECT tbl_temp1.fld_order_id
FROM tbl_temp1 WHERE tbl_temp1.fld_order_id > 100;
```
也可以通过 `SELECT ... INTO OUTFILE` 和 `CREATE TABLE ... SELECT.` 达到同样目的

### 更新

当获取表达式中的一个列，而这个列也将被更新时，MySQL 总是使用列的当前值，例如：
```mysql
UPDATE t1 SET col1 = col1 + 1, col2 = col1;
```
MySQL 执行上面的语句后，col1 和 col2 的值相等，标准 SQL 则不等

??? 测试确认

### 外键

还未整理，但必须整理....
1.8.2.3
???

### 注释

**注释1**

MySQL 除了支持标准 SQL 使用的 C 语言风格的注释： `/* this is a comment */`, 还支
持 `/*![version number] MySQL 扩展代码 */`

**注释2**

标准 SQL 使用的 `--` 开头表示注释，但自动生成查询时可能出现错误，如：
```mysql
UPDATE account SET credit=credit-payment
```
如果 payment 为负数 -3，则语句变为 
```mysql
UPDATE account SET credit=credit--3
```
3 被注释掉了，这个例子说明了以 `--` 表示注释可能导致严重的后果

所以 MySQL 支持的是 `--` 的变种，即 `--` 后必须跟一个空格或一个控制字符如换行才
算注释

在命令行客户端里 MySQL 会忽略所有以 `--` 开头的行

**注释3**

MySQL 注释还包括 `#`

### 约束

MySQL 既可以使用非事务表，也可以使用支持回滚的事务表，因为这个原因，约束处理和其
他 DBMS 系统不一样。

MySQL 的基本理念是在解析语句阶段，对检测到的任何异常尽量生成一个错误，而在执行语
句阶段，尽量从发生的错误中恢复。目前大部分情况是这样，并不是全部。

The options MySQL has when an error occurs are to stop the statement in the
middle or to recover as well as possible from the problem and continue.
当出现错误时，MySQL 处理错误的选项包括停止中间的语句，或者尽可能地从问题中恢复并
继续。

By default, the server follows the latter course. This means, for example, that
the server may coerce invalid values to the closest valid values.
默认情况下，服务器遵循后一种方法。这意味着，例如，服务器可能会将无效的值强制到最
接近的有效值。

Several SQL mode options are available to provide greater control over handling
of bad data values and whether to continue statement execution or abort when
errors occur.
有几种 SQL 模式选项可用来提供对坏数据值处理的更大的控制，以及在出现错误时是否继
续执行语句或中止。

Using these options, you can configure MySQL Server to act in a more traditional
fashion that is like other DBMSs that reject improper input.
使用这些选项，您可以配置 MySQL 服务器以更传统的方式进行操作，就像其他拒绝不正确
输入的 dbms 一样。

The SQL mode can be set globally at server startup to affect all clients.
Individual clients can set the SQL mode at runtime, which enables each client to
select the behavior most appropriate for its requirements.
SQL 模式可以在服务器启动时设置全局选项，这样会应用到所有客户端。个别客户端可以在
运行时设置 SQL 模式，这使得每个客户端能够选择最适合其需求的行为。

#### 主键和惟一索引约束

还未整理，但必须整理....
1.8.3.1
???

#### 外键约束

还未整理，但必须整理....
1.8.3.2
???

#### 对无效数据的约束

还未整理，但必须整理....
1.8.3.3
???

#### 枚举和集合约束

还未整理，但必须整理....
1.8.3.4
???

## 开发 MySQL 使用的工具

* 自由软件基金会（Free Software Foundation）的编译器 gcc，调试器 gdb 和 libc 库

* 自由软件基金会和 XEmacs 开发团队开发的 XEmacs 

* Julian Seward 开发的一个出色的内存检查工具 valgrind

* Dorothea Lütkehaus and Andreas Zeller 开发的 gdb 图形前端工具 DDD (The Data Display Debugger)
