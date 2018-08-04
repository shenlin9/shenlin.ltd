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

`mysql` 系统数据库包含了几个授权表，里面含义用户账户和其权限的信息，此节描述这些
表。关于其他的系统表，参考：Section 5.3, “The mysql System Database”.

为了操作授权表的内容，通常可以通过使用诸如 `CREATE USER`、`GRANT` 和 `REVOKE` 这
样的账户管理语句来设置账户和控制每个帐户的特权来间接地修改它们。
参考：See Section 13.7.1, “Account Management Statements”.

注意：
不鼓励直接使用 `INSERT`, `UPDATE`, `DELETE` 语句修改授权表，服务器可以自由地忽略
由于这些修改而变得畸形的行。

从 MySQL 5.7.18 起，对于修改了授权表的任何操作，服务器会检查是否表结构符合预期，
如果没有则抛出一个错误，`mysql_upgrade` 必须运行以更新表到预期的结构。

下面的讨论描述了授权表的底层结构，以及服务器在与客户交互时如何使用它们的内容。

包含了授权信息的表：

* user: 用户账户，全局特权，其他非权限列
* db: 数据库级特权
* tables_priv: 表级特权
* columns_priv: 列级特权
* procs_priv: 存储过程和函数特权
* proxies_priv: 代理用户特权

每个授权表都包含了范围列和权限列：

* 范围列确定了表里每行适用的上下文范围

例如:

`user` 表里某行的 `Host` 为 `h1.example.net` `User` 为 `bob` 适用于由指定了用户
名为 `bob` 的客户端主机 `h1.example.net` 对服务器发起的身份验证连接。

类似的，`db` 表里的某行 `Host` 为 `h1.example.net`，`User` 为 `bob`，`Db` 为
`reports` 表示指定用户 `bob` 从主机 `h1.example.net` 连接到 `reports` 数据库

`tables_priv` 和 `columns_priv` 表包含的范围列指明了每行适用于哪个表或表/列组合

`procs_priv` 范围列指明了每列适用于的存储例程

* 特权列指明了一行授予的是哪种特权；也就是说哪种操作是允许执行的。

服务器组合各种授权表里的信息来形成一个用户的完整权限的描述，Section 6.2.5, “
Access Control, Stage 2: Request Verification” 描述了此规则

注意：
因为任何全局特权都是所有数据库的特权，所以任何全局特权都使得一个用户可以通过
`SHOW DATABASES` 语句或者通过 `SCHEMA` 表的 `INFORMATION_SCHEMA`查看所有数据库名

**服务器以下列方式使用授权表：**

* `user` 表的范围列决定了是拒绝还是允许进入的连接。对于允许的连接，任何在 `user`
  表里授予的权限：都表明了用户的全局权限，都适用于服务器上的所有数据库。

* `db` 表的范围列确定了哪个用户可以从哪个主机访问哪个数据库。权限列确定了允许的
  操作。数据库级的权限适用于数据库和库内的所有对象，例如表和存储过程。

* `tables_priv` 和 `columns_priv` 表类似于 `db` 表，但更细化。它们分别适用于表级
  和列级，授予表级的权限适用于表和表的所有列，授予列级的权限则只适用于某个指定的
  列。

* `procs_priv` 表适用于存储例程（过程和方法），授予例程级的权限只适用于某个存储
  过程或方法。

* `proxies_priv` 表指明了哪个用户可以为其他用户扮演代理的角色，并且一个用户能否
  授予其他用户 `PROXY` 权限。

服务器在访问控制的第一阶段和第二阶段都要用到 `user` 和 `db` 表，参见：Section
6.2, “The MySQL Access Privilege System”

`db`   表的范围列：Host, User, Db
           权限列：

`user` 表的范围列：Host, User
           权限列：

....
....
暂时省略各表的列....
....
....

* plugin 和 authentication_string

`user` 表的 `plugin` 和 `authentication_string` 列存储的分别是验证插件和凭证信息
，服务器使用一个账号行的 `plugin` 列命名的插件来验证尝试连接到此账户的连接。

`plugin` 列一定不能为空. 服务器在启动和运行执行 `FLUSH PRIVILEGES` 时会检测
`user` 表的行，对于任何 `plugin` 列为空的行，服务器将会在错误日志里写入一条警告
信息，格式如下：

```
[Warning] User entry 'user_name'@'host_name' has an empty plugin
value. The user will be ignored and no one can login with this user
anymore.
```

解决此问题，参考：Section 6.5.1.3, “Migrating Away from Pre-4.1 Password
Hashing and the mysql_old_password Plugin”.

* password_expired

`password_expired` 列允许 DBAs 使得账户密码过期，并要求用户重置密码，此列的默认
值是 `N` ，但可以通过 `ALTER USER` 语句修改为 `Y`。

账户密码过期后，在随后的连接到服务器的此账户执行的所有操作都会产生一个错误，直到
用户通过 `ALTER USER` 语句创建一个新的账户密码。

密码过期后，是可以将密码再次重置为当前值的，但更好的策略是换一个不同的密码。

* password_last_changed 

`password_last_changed` 是一个时间戳列，指明了密码的最后修改时间，对于使用 MySQL
内置身份验证方法的帐户（使用 `mysql_native_password` 或 `sha256_password` 插件的
账户），该值是非 NULL 的，对于使用外部身份验证系统验证的账户，值是 NULL

`password_last_changed` 通过创建账户的语句或更改账户密码的语句如 `CREATE USER`,
`ALTER USER`, `SET PASSWORD`, `GRANT` 被更新

* password_lifetime 

`password_lifetime` 按天数指明了账户密码的生命期，如果使用
`password_last_changed` 列评估密码过了生命期，当客户端使用帐户连接时，服务器会提
示密码过期。

0 表示禁用密码自动过期
大于 0 的 N 值表示密码必须每 N 天更换一次
缺省的 NULL 值，则表示使用由系统变量 `default_password_lifetime` 定义的全局过期
策略

* account_locked 

`account_locked` 指明了账户是否被锁定 (参考 Section 6.3.11, “User Account Locking”).

在访问控制的第二阶段，服务器执行请求验证以确保每个客户端要执行的请求具有足够的权限，除了 `user` 和 `db` 授权表，服务器还可能调阅 `tables_priv` 和 `columns_priv` 这两个由请求涉及到的表。后两个表提供了在表级和列级更细化的权限控制。

`tables_priv` 和 `columns_priv` 表的列如下：

.....
.....
Table 6.4 tables_priv and columns_priv Table Columns
.....
.....

`Timestamp` 和 `Grantor` 列分别被设置为当前时间戳和当前用户值，但是没有使用。

为了验证涉及到存储例程的请求，服务器会查阅 `procs_priv` 表，此表的列如下：

.....
.....
Table 6.5 procs_priv Table Columns
.....
.....

`Routine_type` 列是一个枚举列，值是 `FUNCTION` 或 `PROCEDURE`，指明了行所引用的
例程的类型。本列使得可以分别为一个函数和一个同名的过程授予特权。

`Timestamp` 列和 `Grantor` 列未被使用

**proxies_priv**

`proxies_priv` 记录了代理账户的信息，包括的列：

* Host, User: 代理账户，即具有 `PROXY` 代理权限的账户
* Proxied_host, Proxied_user: 被代理的账户
* Grantor, Timestamp: 未使用
* With_grant: 拥有 `PROXY` 代理权限的用户是否可以把此权限授予其他账户

账户能够对其他账户授予 `PROXY` 权限，则 `proxies_priv` 表的 `With_grant`列的值必
须为 1，且 `Proxied_host` 和 `Proxied_user` 列设置了账户，

For an account to be able to grant the PROXY privilege to other accounts, it must have a row in the proxies_priv table with With_grant set to 1 and Proxied_host and Proxied_user set to indicate the account or accounts for which the privilege can be granted.

例如，在 MySQL 安装过程中创建的 `'root'@'localhost'` 账户在 `proxies_priv` 表中
有一行: `''@''`，表示允许为所有用户和所有主机授予 `PROXY` 代理特权。这使得
`root` 可以建立代理账户，并将授权委托给其他帐户来建立代理账户。
参考： Section 6.3.10, “Proxy Users”.

授权表里的范围列包含字符串，默认值都是空字符串，下表显示的是每列允许的字符数量:

Table 6.6 授权表范围列长度

    列名                        允许的最多字符数
    ---------------------------------------------
    Host, Proxied_host          60
    User, Proxied_user          32
    Password                    41
    Db                          64
    Table_name                  64
    Column_name                 64
    Routine_name                64

`User`, `Proxied_user`, `Password`, `authentication_string`, `Db` 和
`Table_name` 区分大小写

`Host`, `Proxied_host`, `Column_name`, `Routine_name` 不区分大小写

`user` 和 `db` 表的每个权限为一个单独的列，数据类型是枚举 `ENUM('N','Y')`，默认
值是 `'N'`，换句话说，每个权限都可以启用或禁用。


`tables_priv`, `columns_priv` 和 `procs_priv` 表里的权限列数据类型为集合 `SET`
，列值可以为权限的组合，只有组合里包含的权限才是启用的：

Table 6.7 集合类型权限列取值

    表名            列名            可使用的集合元素
    --------------------------------------------------
    tables_priv     Table_priv      'Select', 'Insert', 'Update', 'Delete',
                                    'Create', 'Drop', 'Grant', 'References',
                                    'Index', 'Alter', 'Create View', 'Show view',
                                    'Trigger'

    tables_priv     Column_priv     'Select', 'Insert', 'Update', 'References'

    columns_priv    Column_priv     'Select', 'Insert', 'Update', 'References'

    procs_priv      Proc_priv       'Execute', 'Alter Routine', 'Grant'


只有 `user` 表指定了管理权限，比如 `RELOAD` 和 `SHUTDOWN`。管理操作是服务器本身
的操作不是特定于数据库的，因此没有理由在其他授权表中列出这些特权。因此，服务器只
需要查询 `user` 表，以确定用户是否可以执行管理操作。

`FILE` 特权也是只在 `user` 表指定了，其本身不是管理特权，但是一个账户在服务器主
机上读写文件的能力是独立于被访问的数据库的。

当服务器启动时，服务器会将授权表的内容读入内存。可以通过 `FLUSH PRIVILEGES` 语句
或执行 `mysqladmin flush-privileges` 或 `mysqladmin reload` 命令来重新加载表。对
授权表的更改何时生效参考：Section 6.2.6,“When Privilege Changes Take Effect”

修改一个账户时，应该确认更改后是想要的效果。检查一个给定账户的特权，使用 `SHOW
GRANTS` 语句，例如，检测用户名为 `bob` 主机名为 `pc84.exmaple.com` 的账户被授予
的特权：
```
SHOW GRANTS FOR 'bob'@'pc84.example.com';
```

显示一个账户的非特权属性，使用 `SHOW CREATE USER` 语句：
```
SHOW CREATE USER 'bob'@'pc84.example.com';
```

## 指定账户名

MySQL 帐户名由一个用户名和一个主机名组成。这允许为具有相同名称的用户创建帐户，这
些用户可以从不同的主机连接。本节描述如何编写账户名，包括特殊值和通配符规则。

在类似 `CREATE USER`, `GRANT`, `SET PASSWORD` 这样的SQL 语句里，账户名遵循下列规则：

* 账户名语法 'user_name'@'host_name'.

* 只包含用户名的账户名等价于 'user_name'@'%'. 例如, 'me' 等价于 'me'@'%'.

* 如果用户名和主机名作为未引用的标识符是合法的，则不需要引号引用它。引号是用来指
  定包含特殊字符（如空格或 `-`）的用户名字符串，或包含特殊字符、通配符（如空格或
  `-`）的主机名字符串，例如 `'test-user'@'%.com'`。

* 引用用户名和主机名作为标识符或字符串，可以使用反引号 ````，单引号`''`，双引号
  `""`，对于字符串音乐或标识符引用 参考：Section 9.1.1, “String Literals”, and
  Section 9.2, “Schema Object Names”.

* 如果引用用户名和主机名，必须分开单独引用。也就是 `'me'@'localhost'`，而不是
  `'me@localhost'`，后者等价于 `'me@localhost'@'%'`

* 引用 `CURRENT_USER` 或 `CURRENT_USER()` 函数等价于指定当前客户端的用户名和主机名为字面意思。

MySQL 在 `mysql` 系统数据库的授权表中存储帐户名时，使用单独的两列分别存储用户名
部分和主机名部分：

* `user` 表中每个账户一行记录，`User` 和 `Host` 列分别存储用户名和主机名，此表也
  表明了账户拥有的全局特权。

* 其他的授权表表明了帐户对数据库和数据库中对象的特权。这些表格也有 `User` 和
  `Host` 列用来存储账户名。这些表格中的每一行与用户表中拥有相同用户名和主机名的
  账户相关联。

* 用于访问检测时，用户名比较是区分大小写的，主机名不区分大小写

关于授权表结构的额外细节，参考：Section 6.2.2, “Grant Tables”.

**用户名和主机名具有特定的特殊值或通配符约定，如下所述**

帐户的用户名部分要么是一个非空值，字面上与进入的连接尝试的用户名相匹配，要么是
与任何用户名匹配的空白值（空字符串）。使用空白用户名的帐户是一个匿名用户。要在
SQL 语句中指定匿名用户，请使用空用户名，如 `''@'localhost'`。

账户的主机名部分可以使用多种格式，也允许通配符:

* 主机值可以为主机名或 IP 地址（IPv4 or IPv6），`'localhost'` 表明了本地主机，
  `'127.0.0.1'` 表明了 IPv4 的环回接口，`'::1'` 表明了 IPv6 的环回接口

* `%` 和 `_` 通配符可以在主机名和 IP 地址中使用，这和使用 `LIKE` 操作符执行的模
  式匹配操作有相同的含义。

  例如：主机值 `'%'` 匹配任何主机名，
        `'%.mysql.com'` 匹配 `mysql.com` 域里的任何主机，
        `'198.51.100.%'` 匹配 `198.51.100` C 级网络的任何主机。

  因为主机值里允许 IP 通配符，有人可能会利用这个功能，如命名主机为：
  `198.51.100.somewhere.com`，为了挫败这种尝试，MySQL 在以数字和点开头的主机名上
  不执行匹配。

  例如：一个主机被命名为 `1.2.example.com`，则此主机名永远不会匹配账户的主机名部
  分。一个 IP 通配符值只能匹配 IP 地址，而不是主机名。

* 对于指定了 IPv4 地址的主机值，可以使用子网掩码来指示多少个地址位用于网络号，语
  法是 `host_ip/netmask`（子网掩码符号不能用于 IPv6 地址）。例如:
  ```
  CREATE USER 'david'@'198.51.100.0/255.255.255.0';
  ```

  假设客户端主机 IP 地址为 `client_ip`，则 david 可以从满足下列条件的任何客户端主机链接
  ```
  client_ip & netmask = host_ip
  ```

  也就是说，对于上面所示的 `CREATE USER` 语句：
  ```
  CREATE USER 'david'@'198.51.100.0/255.255.255.0';
  ```
  则
  ```
  client_ip & 255.255.255.0 = 198.51.100.0
  ```
  即满足此条件的 IP 地址范围从 `198.51.100.0` 到 `198.51.100.255`。

  子网淹没通常开始的位设置为 1，后面的位设置为 0，例如：
  * 198.0.0.0/255.0.0.0: 198 A 级网络的任何主机
  * 198.51.100.0/255.255.0.0: 198.51 B 级网络的任何主机
  * 198.51.100.0/255.255.255.0: 198.51.100 C 级网络的任何主机
  * 198.51.100.1: 只有此特定 IP 地址的主机


服务器把用户账户里的主机名部分和系统 DNS 解析器根据客户端主机名或 IP 地址返回的
对应的值执行匹配，
The server performs matching of host values in account names against the client
host using the value returned by the system DNS resolver for the client host
name or IP address.

除了使用子网掩码指定账户的主机值外，服务器执行比较时按字符串匹配，即使账户主机值
给出的是 IP 地址，
Except in the case that the account host value is specified using netmask
notation, the server performs this comparison as a string match, even for an
account host value given as an IP address.

这表示应该按 DNS 使用主机名的格式指定账户的主机名的值，下面是一些需要注意的问题举例：

* 假设本地网络上的主机有一个完全限定的名称 `host1.example.com`。如果 DNS 查找返回主机的名称为 `host1.example.com`，请在帐户主的机值部分使用该名称。如果 DNS 只返回 `host1`，则在帐户主的机值部分使用 `host1`。

* 如果 DNS 将给定的主机返回 IP 地址 `198.51.100.2`，那么它将匹配主机值部分为 `198.51.100.2` 的账户，而不是主机值部分为 `198.051.100.2` 的账户。类似地，它将与主机部分模式为 `198.51.100.%` 的帐户相匹配，而不匹配 `198.051.100.%`。

为了避免类似的问题，建议您检查 DNS 返回主机名和地址的格式。在 MySQL 帐户名称中使用相同格式的值。

## 访问控制，第 1 阶段：连接验证

当你尝试链接到一个 MySQL 服务器时，服务器接受或拒绝链接基于这些条件：

* 你的身份以及你是否可以通过提供正确的密码来验证你的身份
* 你的账户是否被锁住了

服务器首先检查凭证，然后检查帐户锁定状态。无论哪个步骤失败服务器都会完全拒绝您
的访问。否则，服务器会接受连接，然后进入第二阶段并等待请求。

使用 `user` 表的三个范围列（`Host`,`User`,`authentication_string`）验证凭证，验
证锁定状态使用 `user` 表的 `account_locked` 列，

只有当用户表中某些行的 `Host` 和 `User` 列与客户端主机名和用户名匹配，客户端提供
了该行指定的密码，并且 `account_locked` 值为 `N`，服务器才接受连接。

账户锁定状态可以通过 `ALTER USER` 语句更改。
允许的主机和用户值的规则参考： Section 6.2.3, “Specifying Account Names”. 

你的身份基于两个信息：
* 你从哪个客户端主机连接
* 你的 MySQL 用户名

如果 `User` 列值是非空的，那么传入的连接中的用户名必须精准匹配。如果 `User` 的值
是空的，那么它将匹配任何用户名。

如果与传入连接相匹配的 `user` 表行是一个空白的用户名，那么用户就被认为是一个没有
名称的匿名用户，而不是客户实际指定了名称的用户。这表示在接下来的连接过程中（即在
阶段 2 中）所有更进一步访问检查都使用空白用户名。

`authentication_string` 列可以为空，这里不是通配符也不意味着匹配任何密码。它表示
用户连接时必须不能指定密码。

如果服务器验证客户端时使用了插件，插件实现的验证方法可能使用或者可能不使用
`authentication_string` 列里的密码。这种情况下，也可以使用外部密码对服务器进行身
份验证。

`user` 表中的非空白的 `authentication_string` 值表示加密的密码。MySQL 不以明文形
式存储密码以防有人可以看到。相反，试图连接的用户提供的密码是加密的（使用由帐户身
份验证插件实现的密码散列方法），接下来被加密的密码在的连接过程中用于验证密码是否
正确。这是在没有经过连接的加密密码的情况下完成的（This is done without the
encrypted password ever traveling over the connection.）。参见 Section 6.3.1, “
User Names and Passwords”

从 MySQL 的角度来看，加密的密码是真正的密码，所以你不应该让任何人对它有权限。特
别是，不要让非管理用户拥有对 `mysql` 数据库中表的读取权。

下表展示了 `user` 表中 `User` 列和 `Host` 列的各种组合如何应用于传入连接:

User值   Host值           允许的连接
'fred'  'h1.example.net'  fred, connecting from h1.example.net
''      'h1.example.net'  Any user, connecting from h1.example.net
'fred'  '%'               fred, connecting from any host
''      '%'               Any user, connecting from any host
'fred'  '%.example.net'   fred, connecting from any host in the example.net domain
'fred'  'x.example.%'     fred, connecting from x.example.net, x.example.com, x.example.edu, and so on; this is probably not useful
'fred'  '198.51.100.177'  fred, connecting from the host with IP address 198.51.100.177
'fred'  '198.51.100.%'    fred, connecting from any host in the 198.51.100 class C subnet
'fred'  '198.51.100.0/255.255.255.0'        Same as previous example

进入的连接通过主机名和用户名可能匹配 `user` 表里的多行，例如上面的例子。当有多个
匹配行时，服务器按下面的规则确定使用哪个：

* 服务器把 `user` 表读入内存时会对行进行排序
* 客户端连接时，服务器会按排好的顺序匹配行
* 服务器使用匹配到的第一个行

The server uses sorting rules that order rows with the most-specific Host values
first.  Literal host names and IP addresses are the most specific. (The
specificity of a literal IP address is not affected by whether it has a netmask,
so 198.51.100.13 and 198.51.100.0/255.255.255.0 are considered equally
specific.)

服务器排序规则：

* 先按主机排序
    * 首先使用最具体的主机值来排列各行。字面意义的主机名和 IP 地址是最具体的。（
      字面意义的 IP 地址的特殊性不受其是否有网络掩码的影响，因此 `198.51.100.13`
      和`198.51.100.0/255.255.255.0` 被认为是同等具体的。）
    * 模式 `%` 的意思是“任何主机”，是最不具体的。
    * 空字符串也意味着任何主机，但排序在 `%` 之后。
* 再按用户排序
    * 具有相同 `Host` 值的行首先使用最具体的 `User` 值排序
    * 空白 `User` 值意味着“任何用户”是最不具体的
* 对于 `Host` 和 `User` 都同等具体的行，顺序是不确定的。

要了解这是如何工作的，假设 `user` 表是这样的：

    +-----------+----------+-
    | Host      | User      | ...
    +-----------+----------+-
    | %         | root      | ...
    | %         | jeffrey   | ...
    | localhost | root      | ...
    | localhost |           | ...
    +-----------+----------+-

服务器把表读入内存后，按上述规则排序：

    +-----------+----------+-
    | Host      | User      | ...
    +-----------+----------+-
    | localhost | root      | ...
    | localhost |           | ...
    | %         | jeffrey   | ...
    | %         | root      | ...
    +-----------+----------+-

客户端连接时，服务器按顺序匹配行，并使用第一个匹配的行。

例如：jeffrey@localhost会匹配第2行和第3行，则使用第2行，所以从这里可以看出，虽然
客户端明确的指定了用户jeffrey，但匹配到的却是匿名用户，因为要先匹配主机。这也解
释了一种常见的误解，即客户端明确的给定了用户名时，服务器会优先匹配 `User` 为此用
户名的行，这是错误的。

所以，有时以指定用户连接到服务器后，发现你的权限和所期望的不一致，则很可能是被服
务器用其他账户的身份验证和授权的，想要查看自己到底目前使用的是哪个账户的身份，使
用：CURRENT_USER() 方法（参考：Section 12.14 “Information Functions”），其返回
的格式是 `user_name@host_name`，取自 `user` 表的匹配的 `Host` 和 `User` 列。

所以上述例子中的 jefferey 可以执行下列语句查看自己的真正身份：

```mysql
mysql> SELECT CURRENT_USER();
+----------------+
| CURRENT_USER() |
+----------------+
| @localhost     |
+----------------+
```
* 说明 `user` 表里有一 `User` 值为空的行匹配了 jefferey，所以 jefferey 目前为匿名
用户身份。

另一个诊断身份验证问题的方法是手动排序输出 `user` 表查看匹配。

## 访问控制，第 2 阶段：请求验证

在建立客户端和服务器的连接后，服务器进入第二阶段的访问控制。对于用户通过此连接执
行的每个请求，服务器都会判定用户要执行什么操作，然后检测用户是否又足够的权限执行
此操作。授权表里的各权限列即在这时开始发挥作用。这些权限可来自于任何的 `user`,
`db`,`tables_priv`,`columns_priv`,`procs_priv` 表（参考：Section 6.2.2, “Grant
Tables”）。

### user

`user` 表在全局基础上为用户授权，和数据库无关。例如 `user` 表授予了你 `DELETE`
特权，则你可以删除服务器主机上任何数据库里的任何表里的任何行。因此明智的做法是只
对那些需要此特权的用户授予 `user` 表特权，例如数据库管理员。对于其他用户，应该把
`user` 表里的所有特权都赋值为 `N`，然后在更细化的层级进行赋值，即为特定的数据库
、表、列和例程赋值。

### db

`db` 表授予的权限是特定于数据库的权限。此表中的范围列可以使用下列格式：
* 空的 `User` 值匹配匿名用户，非空的 `User` 则匹配其字面意义的用户，在用户名里不
  使用通配符。
* 通配符 `%` 和 `_` 可以用在 `Host` 和 `Db` 列中，用于模式匹配操作时和 `LIKE` 操
  作符的执行效果一样，用于授权时如果要使用它们的字面意义，则需反斜线转义，例如数
  据库名中包含了下划线，则需在 `GRANT` 语句中使用 `\_` 的格式。
* `Host` 值为 `%` 或空表示任何主机
* `Db` 值为 `%` 或空表示任何数据库

服务器读取 `user` 表的同时也会把 `db` 表读入内存并排序，服务器排序 `db` 表基于
`Host`,`Db`,`User` 三个范围列，排序规则和 `user` 表一样，最具体的值放最前面，最
不具体的值放最后面，服务器按排列顺序匹配，并使用第一个匹配到的行。

### tables_priv,columns_priv,procs_priv

`tables_priv`,`columns_priv`,`procs_priv` 表同理分别授予的权限是特定于表、列和例
程的，这些表的范围列的值可以使用下面的格式：
* 通配符 `%` 和 `_` 可以用在 `Host` 列中，用于模式匹配操作时和 `LIKE` 操作符的执
  行效果一样。
* `Host` 值为 `%` 或空表示任何主机
* `Db`,'Table_name','Column_name','Routine_name' 列值不能包含通配符，不能为空

服务器排序 `tables_priv`,`columns_priv`,`procs_priv` 三个表时基于
`Host`,`Db`,`User` 三列，和 `db` 表的排序类似，但更简单，因为只有 `Host` 列可以
包含通配符。

### 验证请求

服务器使用这些排好序的表来验证接受到的每个请求:

* 对于需要管理权限如`SHUTDOWN`,`RELOAD` 一类请求，服务器只需检测 `user` 表中的行
  ，因为只有 `user` 表可以指定管理权限。如果行允许请求的操作则服务器授予权限否则
  就拒绝授权。例如你想执行 `mysqladmin shutdown`，但 `user` 表的行没有授权给你
  `SHUTDOWN` 特权，则服务器连检测 `db` 表都不用就可以拒绝授权给你，因为 `db` 表
  里没有包含`SHUTDOWN_priv`列，所以不必再检测。

* 对于数据库相关的请求（`INSERT`,`UPDATE` 等等），服务器首先在 `user` 表检测用户的全局权限，如果 `user` 表的行允许请求的操作则授权，如果权限不充分，则服务器通过检查 `db` 表来确定用户的特定于数据库的特权：
    * 服务器在 `db` 表里根据 `Host`,`User`,`Db` 列查找匹配：`Host` 和 `User` 列
      分别匹配连接用户的主机名和 MySQL 用户名，`Db` 列匹配用户想要访问的数据库，
      如果没有匹配的行，则拒绝访问。

* 在通过 `db` 表的行确定了被授予的特定数据库特权之后，服务器把这些数据库特权加入
  到通过 `user` 表确定的全局特权中，如果结果允许请求的操作，则授予特权，否则，服
  务器依次检测 `tables_priv` 和 `columns_priv` 中的特定的表特权和列特权，并把这
  些特权加入用户特权，最后允许还是拒绝取决于结果。

* 对于存储例程的操作，服务器使用 `procs_priv` 表检测而不是 `tables_priv` 和
  `columns_priv`

用布尔术语表示，前面描述用户特权的描述可以这样总结
```
global privileges
OR (database privileges AND host privileges)
OR table privileges
OR column privileges
OR routine privileges
```

为什么要这样操作呢？如果最初的 `user` 表里的全局特权不能满足请求的操作，

It may not be apparent why, if the global user row privileges are initially found to be insufficient for the requested operation, the server adds those privileges to the database, table, and column privileges later. 

原因是一个请求可能需要一种类型以上的特权，例如执行 `INSERT INTO ... SELECT` 语句
时，就需要 `INSERT` 和 `SELECT` 两种特权，用户的两种特权可能一个来自 `user` 表另
一个来自 `db` 表。这样用户才有足够的特权去执行请求，但是服务器自身也没法分清哪个
特权来自哪个表，来自两个表的特权必须组合起来。

## 特权变更后何时生效

When mysqld starts, it reads all grant table contents into memory. The in-memory tables become effective
for access control at that point.

If you modify the grant tables indirectly using account-management statements such as GRANT, REVOKE,
SET PASSWORD, or RENAME USER, the server notices these changes and loads the grant tables into
memory again immediately.

If you modify the grant tables directly using statements such as INSERT, UPDATE, or DELETE, your
changes have no effect on privilege checking until you either restart the server or tell it to reload the tables.

If you change the grant tables directly but forget to reload them, your changes have no effect until you
restart the server. This may leave you wondering why your changes seem to make no difference!

To tell the server to reload the grant tables, perform a flush-privileges operation. This can be done by
issuing a FLUSH PRIVILEGES statement or by executing a mysqladmin flush-privileges or
mysqladmin reload command.

A grant table reload affects privileges for each existing client connection as follows:
• Table and column privilege changes take effect with the client's next request.
• Database privilege changes take effect the next time the client executes a USE db_name statement.
• Global privileges and passwords are unaffected for a connected client. These changes take effect only
for subsequent connections.

If the server is started with the --skip-grant-tables option, it does not read the grant tables or
implement any access control. Anyone can connect and do anything, which is insecure. To cause a server
thus started to read the tables and enable access checking, flush the privileges.

Note
Client applications may cache the database name; thus, this effect may not be
visible to them without actually changing to a different database.

## 连接到 MySQL 时的故障排除


