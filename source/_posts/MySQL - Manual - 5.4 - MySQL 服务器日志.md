---
title: MySQL - Manual - 5.4 - MySQL 服务器日志
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 服务器有几个日志可以帮助您了解正在发生的活动

<!--more-->

## 日志类型和写入日志的信息

**错误日志**

开始、运行或停止 mysqld 的问题

**普通查询日志**

已建立的客户端连接和从客户端接收的语句

**二进制日志**

改变数据的语句（也用于复制）

**中继日志**

relay log

从复制主服务器接收到的数据更改

**慢查询日志**

查询花费了超过长时间的查询时间来执行

**DDL 日志 (元数据日志)**

由 DDL 语句执行的元数据操作

##

默认情况下，除了 Windows 上的错误日志之外，没有启用任何日志。

DDL 日志总是在需要时创建，并且没有可配置选项。

默认情况下，服务器为 data 目录中的所有启用日志的数据库记录日志.

通过刷新日志(flushing the logs)，可以强制服务器关闭并重新打开日志文件，或者某些
情况下切换为新的日志文件。

刷新日志可以通过执行 `FLUSH LOGS` 语句进行:
```
shell> mysqladmin --flush-logs
shell> mysqladmin --refresh

shell> mysqldump --flush-logs
shell> mysqldump --master-data
```
另外，当二进制日志的大小达到系统变量 `max_binlog_size` 设置的值时也会被刷新。

可以在运行时控制通用查询和慢查询的日志：启用或禁用日志，更改日志文件名，设置服务
器把日志条目写入到日志表或日志文件，或者都写入。

中继日志只用在从属复制服务器上，为的是保存主复制数据库的数据更改并把它应用到从属
复制服务器。中继日志的内容和设置可参考：Section 16.2.4.1, “The Slave Relay Log”

有关日志安全的信息，参考：Section 6.1.2.3, “Passwords and Logging”

