---
title: MySQL - Manual - 6.2 - MySQL 访问权限系统 - Security
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 访问权限系统介绍

<!--more-->

MySQL 权限系统的主要功能：

1. 验证从指定主机连接到服务器的用户

2. 将此用户与数据库中的权限连接起来，如 SELECT, INSERT, UPDATE, DELETE

3. 额外的功能包括拥有匿名用户的能力，以及为 MySQL 特定的功能授予特权，比如 `LOAD
   DATA INFILE` 和管理操作

有些儿事是不能用 MySQL 权限系统做的：

1. 不能显式地指定一个给定的用户应该被拒绝访问，也就是说，不能明确地匹配一个用户
   然后拒绝连接

2. 不能指定用户有权在数据库中创建、删除表，而不是创建或删除数据库本身。
    You cannot specify that a user has privileges to create or drop tables in a
    database but not to create or drop the database itself.

3. 密码在全局范围内适用于一个帐户。不能将密码与特定的对象关联起来，比如数据库、
   表格或程序。

MySQL 权限系统的用户界面由 SQL 语句组成，如 CREATE USER、GRANT 和 REVOKE。
参考: Section 13.7.1, “Account Management Statements”.

MySQL 服务器在名为 `mysql` 的数据库中的授权表里保存了授权信息，服务器在启动时将
这些表的内容读入内存，并在授权表的内存副本上建立访问控制决策。。
参考：6.2.6, “When Privilege Changes Take Effect”.

MySQL 的权限系统确保了所有的用户只能执行被允许的操作。

当一个用户连接到一个 MySQL 服务器时，用户的身份由用户发起连接的主机和连接时使用
的用户名决定。

当用户在连接后发出请求时，系统根据用户的身份和用户想要做的事情授予权限。

MySQL 在识别用户的时候同时使用主机名和用户名，这是因为没有理由假设一个给定的用户
名属于所有主机上的同一个人，因此不能只使用用户名来识别。

例如从主机 `office.google.com` 连接的用户 `joe` 和从主机 `home.google.com` 连接
的用户 `joe`，虽然用户名相同，但不一定是同一个人，他们只是不同主机上碰巧用户名相
同的两个人，这时，MySQL 可以分别根据不同的主机，给两个 `joe` 授予不同的权限。

查看给定账户的权限，使用 `SHOW GRANTS` 语句：
```
SHOW GRANTS FOR 'joe'@'office.example.com';
SHOW GRANTS FOR 'joe'@'home.example.com';
```

当您运行一个连接到服务器的客户端程序时，MySQL 访问控制涉及两个阶段：

阶段1：服务器根据您的身份以及您是否能通过提供正确的密码来验证您的身份决定是接受
还是拒绝您的连接。

阶段2：假设您可以连接，服务器将检查您发出的每一个语句，以确定您是否有足够的权限
来执行它。例如，如果您试图从数据库中的表中选择行，或者从数据库中删除一张表，那么
服务器将验证您是否拥有该表的 `SELECT` 权限或数据库的 `DROP` 权限。

各阶段的具体情况参考：
Section 6.2.4, “Access Control, Stage 1: Connection Verification”
Section 6.2.5, “Access Control, Stage 2: Request Verification”.

当您连接着服务器时，如果您的权限被您自己或其他人更改了，这些更改并不一定立即对您
要执行的下一个语句生效。有关服务器在什么情况下会重新加载授权表的详细信息，参见：
Section 6.2.6, “When Privilege Changes Take Effect”.

## MySQL 提供的特权

授予 MySQL 账号的权限决定了此账号能执行什么操作。

MySQL 权限在不同的操作级别不同，在不同的上下文环境中也不同：

* 管理特权使用户能够管理 MySQL 服务器的操作。这些权限是全局性的因为它们并不针对
  某个特定的数据库。

* 数据库特权应用于数据库和它内部的所有对象。这些特权可以被授予特定的数据库，或者
  在全局范围内应用于所有数据库。 

* 数据库对象的特权，如表、索引、视图和存储例程，可以授予某个数据库中的特定对象，
  或者授予某个数据库中给定类型的所有对象（例如，数据库中的所有表），或者全局性的
  授予所有数据库中给定类型的所有对象）。

账户的权限信息保存在系统数据库 `mysql` 的下列表里：`user`, `db`, `tables_priv`,
`columns_priv`, `procs_priv`
参考：Section 6.2.2, “Grant Tables”

一些 MySQL 版本会对授权表的结构更改,以添加新的特权或特性。为了确保您可以利用任何
新的功能,每当您升级 MySQL 时,更新您的授权表来拥有当前结构。
参考：Section 4.4.7, “mysql_upgrade — Check and Upgrade MySQL Tables”.

下面展示了在授权和撤销授权语句中使用的权限名称，以及授权表中相关联的列名，以及权
限应用的上下文:

...
xxx
xxx表格暂时略xxx
xxx
...

下面的列表提供了MySQL中可用的特权的一般描述。特定的SQL语句可能有比这里所示的更特
定的特权要求。如果是这样，对该陈述的描述提供了详细信息:

注意下面的 `使得 enables` 和 `需要 needed`

* `ALL` 或 `ALL PRIVILEGES` 说明符都是简写。它代表的是“在给定的特权级别上可用的
  所有特权”（除了 `GRANT OPTION` 特权）。例如，在全局或表级授予 `ALL` 特权，则
  会授予账户所有全局特权或所有表级特权。

* `ALTER` 特权使得用户可以使用 `ALTER TABLE` 语句以修改表结构，`ALTER TABLE` 语
  句同时也需要 `CREATE` 和 `INSERT` 特权。重命名表需要在旧表上有 `ALTER` 和
  `DROP` 特权，在新表上有 `CREATE` 和 `INSERT` 特权

* 修改或删除存储例程（过程和函数）时需要 `ALTER ROUTINE` 特权

* 创建存储例程（过程和函数）时需要 `CREATE ROUTINE` 特权

* `CREATE` 特权使得可以创建新的数据库和表

* 创建、修改或删除表空间和日志文件组需要 `CREATE TABLESPACE` 特权。

* `CREATE TEMPORARY TABLES` 特权使得可以使用 `CREATE TEMPORARY TABLE` 语句来创建
   临时表，在 Session 会话创建临时表之后，服务器不会在表上执行进一步的权限检查，
   创建了此表的会话可以在表上执行任何操作，例如 `DROP TABLE`, `INSERT`,
   `UPDATE`, `SELECT` 等
   参考：Section 13.1.18.3, “CREATE TEMPORARY TABLE Syntax”

* `CREATE USER` 特权使得可以使用下列语句：`ALTER USER, CREATE USER, DROP USER,
  RENAME USER, REVOKE ALL PRIVILEGES`

* `CREATE VIEW` 特权使得可以使用 `CREATE VIEW` 语句

* `DELETE` 特权使得可以从数据库表中删除行。

* `DROP` 特权使得你可以移除已存在的数据库、表和视图。
  为了在一个已分区的表上使用 `ALTER TABLE ... DROP PARTITION` 语句则需要 `DROP` 特权。
  使用 `TRUNCATE TABLE` 也需要 `DROP` 特权。
  如果您将一个 MySQL 数据库的 `DROP` 特权授给了一个用户，那么该用户就可以删除存
  储 MySQL 访问特权的数据库。
  If you grant the DROP privilege for the mysql database to a user, that user
  can drop the database in which the MySQL access privileges are stored.
  ???

* 创建、修改、删除、查看事件调度器的事件需要 `EVENT` 权限

* 执行存储例程（过程和函数）需要 `EXECUTE` 特权。

* `FILE` 特权允许你在服务器主机上使用 `LOAD DATA INFILE`，`SELECT ... INTO
   OUTFILE` 语句和 `LOAD_FILE()` 方法，

  拥有 `FILE` 特权的用户可以读取服务器主机上任何文件，只要这些文件是
  world-readable 或 MySQL 服务器可读的，这意味着此用户可以读取任何数据库目录下的任
  何文件，因为服务器可以访问所有这些文件。

  `FILE` 特权也使得用户可以在 MySQL 服务器有写权限的任何目录里创建新文件，包括服
  务器的数据目录，此目录里包含了特权表的实现文件

  作为一项安全措施，服务器不会覆盖任何已存在的文件。

  从 MySQL 5.7.17 起，使用 `CREATE TABLE` 语句创建表时，如果要使用 `DATA
  DIRECOTRY` 或 `INDEX DIRECTORY` 选项，则是需要 `FILE` 特权的。

  为了限制哪些位置的文件可以被读取和写入，可以设置系统变量 `secure_file_priv` 为
  一个指定的目录，参考：Section 5.1.5, “Server System Variables”.

* `GRANT OPTION` 特权使得你可以把自己所拥有的特权授予其他用户或从其他用户那里移除

* `INDEX` 特权使得你可以创建或移除索引，`INDEX` 特权适用于已存在的表。如果你有某
  个表的 `CREATE` 特权，你可以在 `CREATE TABLE` 语句里包含索引定义。

* 插入特权允许将行插入到数据库中的表中，对于表的维护语句 `ANALYZE TABLE`,
  `OPTIMIZE TABLE`, `REPAIR TABLE`也是需要 `INSERT` 特权的。

* `LOCK TABLES` 特权允许使用显式 `LOCK TABLES` 语句来锁定您拥有 `SELECT` 权限的
  表。这包括使用写入锁，这阻止了其他会话读取锁定的表。

* `PROCESS` 显示在服务器内执行的线程的信息（也就是说，关于 Session 会话执行的
  SQL 语句的信息）。

  `PROCESS` 权限还使得用户可以使用 `SHOW PROCESSLIST`, `mysqladmin processlist`
  命令来查看属于其他用户的线程（用户总是可以查看自己的线程）

  `PROCESS` 权限还使得用户可以使用 `SHOW ENGINE`

* `PROXY` 特权使得用户可以伪装成另一个用户，参考：Section 6.3.10, “Proxy Users”

* 创建外键约束需要父表的 `REFERENCES` 特权。

* `RELOAD` 特权使得可以使用 `FLUSH` 语句，同时也使得可以使用和其等效的
  `mysqladmin` 命令：`flush-hosts, flush-logs, flush-privileges, flush-status,
  flush-tables, flush-threads, refresh, reload`

  `reload` 命令告诉服务器重新把授权表载入内存。

  `flush-privileges` 是 `reload` 的同义词

  `refresh` 命令关闭日志文件并重新打开，并刷新所有表 (flushes all tables)。

  其他的 `flush-xxx` 命令的功能和 `refresh` 相似，但更有针对性，在某些情况下可能
  更可取。例如，如果只想刷新日志文件，`flush-log` 是一个比刷新更好的选择。

* `REPLICATION CLIENT` 特权使得可以使用 `SHOW MASTER STATUS`, `SHOW SLAVE STATUS`, `SHOW BINARY LOGS` 语句

* `REPLICATION SLAVE` 特权应该授权给连接到当前服务器作为主服务器的从服务器使用的账户，
  没有此特权，则从服务器不能请求主服务器数据库上已经完成的更新。
  Without this privilege, the slave cannot request updates that have been made to
  databases on the master server.
  没有此特权，则从服务器不能请求主服务器数据库上已经完成的更新。
  没有此特权，则从服务器不能请求对主服务器上的数据库进行更新。???

* `SELECT` 权限使得你可以从数据库表里检索出行。只有当使用 `SELECT` 语句实际从表里
   检索行时才必须 `SELECT` 特权。有些 `SELECT` 语句不访问表且不需要任何数据库的允
   许也可执行，例如把 `SELECT` 作为一个简单的计算器：

   ```sql
   SELECT 1+1;
   SELECT PI()*2;
   ```

  对于读取列值的其他语句也是需要 `SELECT` 权限的。例如：在 `UPDATE` 语句里赋值语
  句 `col_name=expr` 的右侧引用的列(columns referenced on the right hand side of
  col_name=expr assignment)需要 `SELECT` 权限，在 `DELETE`, `UPDATE` 语句的
  `WHERE` 子句里的列名需要 `SELECT` 权限

  表、视图和 `EXPLAIN` 一起使用时也需要 `SELECT` 权限，包括任何基本表的视图
  (underlying tables of views.)

* `SHOW DATABASES` 特权使得账户可以通过执行 `SHOW DATABASE` 语句查看数据库名，
  没有此特权的账户只能看见他们有一些儿特权的数据库，如果服务器启动时使用了
  `--skip-show-database` 选项，则不能使用 `SHOW DATABASE` 语句

  注意任何全局特权都是数据库的特权。
  Note that any global privilege is a privilege for the database

* `SHOW VIEW` 特权使得可以使用 `SHOW CREATE VIEW` 语句，当视图和 `EXPLAIN` 语句
  一起使用时也需要 `SHOW VIEW` 特权

* `SHUTDOWN` 特权使得可以使用 `SHUTDOWN` 语句，`mysqladmin shutdown` 命令和
  `mysql_shutdown()` C 语言的 API 方法

* `SUPER` 特权支持执行这些操作和服务器行为：

    * 通过修改全局系统变量来更改配置。一些系统变量设置 Session 会话值时也需要
      `SUPER` 特权，在变量描述中会注明。例子包括 `binlog_format`, `sql_log_bin`,
      `sql_log_off`

    * 允许修改全局事务特性，参考： Section 13.3.6, “SET TRANSACTION Syntax”

    * 允许启动和停止在从属服务器上的复制，包括群复制。

    * 允许使用 `CHANGE MASTER TO` 和 `CHANGE REPLICATION FILTER` 语句.

    * 允许通过 `PURGE BINARY LOGS` 和 `BINLOG` 语句控制二进制日志.

    * 允许在执行视图或存储程序时设置有效的授权 ID，拥有此特权的用户可以在视图或
      存储程序的DEFINER属性中指定任何账户

    * 允许使用 `CREATE SERVER`, `ALTER SERVER`, `DROP SERVER` 语句.

    * 允许使用 `mysqladmin debug` 命令.

    * Enables InnoDB key rotation.

    * 允许通过 `DES_ENCRYPT()` 方法读取 DES 密钥文件

    * 允许执行版本令牌用户定义函数 Enables execution of Version Tokens user-defined functions.

    * 允许控制不被允许的的客户端连接到非 `SUPER` 账户 Enables control over client connections not permitted to non-SUPER accounts:

        * 允许使用 `KILL` 语句或 `mysqladmin kill` 命令来终止属于其他用户的线程
        （自己的进程总是可以终止）

        * 即使已经达到了系统变量 `max_connections` 限制的最大连接数，服务器仍然
          可以接受一个来自 `SUPER` 的客户端连接

        * 即使系统变量 `read_only` 已经启用，仍可以执行更新操作。适用于执行表更
          新或使用账户管理语句 `GRANT`,`REVOKE`

        * 当 `SUPER` 客户端连接时，服务器不知晓系统变量 `init_connect` 的内容

        * 脱机模式的服务器A server in offline mode (启用了 `offline_mode`) does not terminate SUPER client connections at the next client request, and accepts new connections from SUPER clients.

    如果启用二进制日志，您可能还需要 `SUPER` 特权来创建或更改储存过程，参考：
    Section 23.7, “Binary Logging of Stored Programs”.

* `TRIGGER`

`TRIGGER` 特权允许执行触发器操作，用户必须拥有某个表的 `TRIGGER` 特权，才可以在
此表上进行创建、删除、执行、显示触发器操作。

当一个触发器被触发时（被某个用户在和触发器关联的某个表上执行 `INSERT`, `UPDATE`,
`DELETE` 语句），执行触发器时要求定义了此触发器的用户必须当时仍然有 `TRIGGER` 特
权

* `UPDATE`

`UPDATE` 特权使得可以更新数据库表里的行。

* `USAGE`

`USAGE` 特权表示 "没有特权"，它在全局级别上和 `GRANT` 搭配使用，用来在不指定特定
帐户特权的情况下修改帐户属性，如资源限制或SSL特征

`SHOW GRANTS` 显示的 `USAGE` 表明帐户在特权级别没有特权。SHOW GRANTS displays
USAGE to indicate that an account has no privileges at a privilege level.

应该只向一个帐户授予它所需要的特权。在授予文件和管理权限时，应该特别小心：

*  `FILE`

`FILE` 特权可能被滥用于把 MySQL 服务器能在主机上读取的任何文件读取到数据库表，包
括所有 word-readable 文件和服务器数据目录里的文件，然后表里的内容再通过 `SELECT`
语句传送给客户主机

*  `GRANT OPTION`

`GRANT OPTION` 特权使得用户可以把自己的特权授予其他用户，两个都拥有 `GRANT
OPTION` 特权且其他特权不相同的用户可以合并特权

*  `ALTER`

`ALTER` 特权可能被用于通过重命名表名来颠覆权限系统

*  `SHUTDOWN`

`SHUTDOWN` 特权可能被滥用以通过终止服务器的方式来完全拒绝向其他用户提供服务

*  `PROCESS `

`PROCESS` 特权可以被用来查看正在执行的语句的纯文本，包括了正在设置密码或更改密码
的语句文本。

*  `SUPER`

`SUPER` 特权用来终止其他会话或改变服务器的执行方式

授予 `mysql` 数据库本身的特权可以用来更改密码和其他访问特权信息。
密码以加密方式存储，所以恶意用户不能直接获得明文密码，但是，对 `user` 表
`authentication_string ` 列有写权限的用户可以改变账号密码并连接到服务器。

## 授权表

## 指定账户名

## 访问控制，第 1 阶段：连接验证

## 访问控制，第 2 阶段：请求验证

## 当特权变更生效时

## 连接到 MySQL 时的故障排除
