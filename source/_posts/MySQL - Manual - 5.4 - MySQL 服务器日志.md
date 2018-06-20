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

## 5.4.1 选择一般查询和慢速查询日志输出目的地

对于一般查询和慢查询，日志输出目的地可以为日志文件，也可以为 mysql 数据库中的
general_log 和 slow_log 表。可以同时输出到日志文件和表，也可以二选其一输出。

### 启动服务器时的日志控制

`--log-output=value,...`

指定日志输出的目的地

此选项本身并不能启用日志

选项值可以是 `TABLE`    表示写入日志到表
             `FILE`     表示写入日志到文件
             `NONE`     表示不写入日志到表或文件
             多个值时用逗号分隔，如 `TABLE,FILE`
             `NONE` 出现时优先级高于其他两个

如果此选项被省略，则默认的日志目的地是文件

`--general-log={0|1}`

启用或禁用通用查询日志

`--general-log-file=`

指定普通查询日志输出的具体文件名，替代默认的日志文件

`--slow-query-log={0|1}`

启用或禁用慢查询日志

`--slow-query-log-file=`

指定慢查询日志输出的具体文件名，替代默认的日志文件

### 运行时的日志控制

上述的几个系统变量在运行时都可以更改

但表示启用禁用的 1 或 0 要改为 ON 和 OFF ???确认一下

如果要启用或禁用当前连接会话的普通查询日志，设置会话级变量 `sql_log_off` 为 `ON`
或 `OFF`

### 日志表

相对于日志文件来说，日志表具有下面的优点和特点

#### 优点

日志内容可以通过 SQL 语句访问，对客户端来说访问日志表比日志文件更方便

日志可以通过连接到 MySQL 服务器的客户端远程访问，而无需登录 OS 和访问文件系统

日志条目都是统一的标准格式，查看日志表的结构：
```sql
SHOW CREATE TABLE mysql.general_log;
SHOW CREATE TABLE mysql.slow_log;
```

#### 特点

一般来说，日志表的主要目的是为用户提供一个接口，以观察服务器的运行时执行，而不是
干扰其运行时执行。

**日志表规则**

写入日志表的条目不会写入二进制日志，因此日志不会复制到从服务器

刷新日志表或日志文件，请分别使用 `FLUSH TABLES` 和 `FLUSH LOGS`

不允许对日志表进行分区

mysqldump 转储包含用于重新创建这些日志表的语句，以便在重新加载转储文件之后不会丢
失这些表。日志表内容不会被转储

**日志表允许和禁用的操作**

CREATE TABLE、ALTER TABLE 和 DROP TABLE 是日志表上的有效操作

对于 ALTER TABLE 和 DROP TABLE，日志表必须没有被使用且被禁用

TRUNCATE TABLE 是日志表上的有效操作，可以用来使日志表条目过期

CHECK TABLE 是日志表上的有效操作

LOCK TABLES 不能用于日志表

INSERT, DELETE, UPDATE 不能用于日志表，这些操作只允许服务器自己内部进行

FLUSH TABLES WITH READ LOCK 和系统变量 read_only 对日志表无效，服务器总是可以写
入日志表

**日志表存储引擎**

默认情况下，日志表使用 CSV 存储引擎，格式是以逗号分隔的值，对于有权限访问包含日
志表数据的 CSV 文件的用户来说，这些 CSV 文件是很容易导入到支持 CSV 输入的程序的

日志表可以修改为使用 MyISAM 引擎，但对于使用中的日志表不能使用 `ALTER TABLE` 修
改，日志表必须先被禁用然后再修改。

日志表只能使用 CSV 和 MyISAM 这两种引擎。

修改日志表引擎策略举例：
```sql
SET @old_log_state = @@global.general_log;
SET GLOBAL general_log = 'OFF';
ALTER TABLE mysql.general_log ENGINE = MyISAM;
SET GLOBAL general_log = @old_log_state;
```

**重命名日志表**

RENAME TABLE 是日志表上的有效操作。

重命名日志表策略举例：
```sql
USE mysql;
DROP TABLE IF EXISTS general_log2;
CREATE TABLE general_log2 LIKE general_log;
RENAME TABLE general_log TO general_log_backup, general_log2 TO general_log;
```
