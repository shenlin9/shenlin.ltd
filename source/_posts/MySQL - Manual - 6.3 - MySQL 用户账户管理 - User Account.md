---
title: MySQL - Manual - 6.3 - MySQL 用户账户管理 - User Account
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 用户账户管理介绍

<!--more-->

此节描述如何为 MySQL 服务器设置客户端账户，主题包括：

* MySQL 里使用的账户名和密码的含义，以及和你的操作系统使用的用户名和密码的比较
* 如何建立新账户和移除已存在账户
* 如何修改密码
* 安全使用密码的参考

关于用户管理的 SQL 语句的语法和用法，参考：Section 13.7.1, “Account Management
Statements”

## 6.3.1 User Names and Passwords

MySQL 在 `mysql` 系统数据库的 `user` 表中存储账户。账户的定义是根据用户名和客户
端主机或用户可以从哪台主机连接到服务器，`user` 表里的账户信息参考：Section
6.2.2, “Grant Tables”

账户也可能有密码。MySQL 支持验证插件，所以账户验证使用外部的验证方法也是可能的
。参考：Section 6.3.9, “Pluggable Authentication”.

在 MySQL 中和在操作系统中使用用户名、密码的方式有几个区别：

* MySQL 用于认证目的的用户名与 Windows 或 Unix 使用的用户名（登录名）没有任何关系。在 Unix 上，大多数 MySQL 客户端默认使用当前 Unix 用户名作为 MySQL 用户名登录，但这只是为了方便起见。这个默认设置可以很容易地覆盖掉，因为客户端程序允许用 `-u` 或 `--user` 选项指定任何用户名。这意味着任何人都可以尝试使用任何用户名连接到服务器，因此除非所有 MySQL 帐户都有密码，否则您不能以任何方式使数据库安全。任何一个人为没有设置密码的账户指定一个用户名就能够成功地连接到服务器。

* MySQL 用户名最多可以有 32 个字符的长度，操作系统用户名可能是不同的最大长度。例如，Unix 用户名通常被限制为 8 个字符。

    Warning

    The limit on MySQL user name length is hardcoded in MySQL servers and
    clients, and trying to circumvent it by modifying the definitions of the tables in the
    mysql database does not work.

    You should never alter the structure of tables in the mysql database in any
    manner whatsoever except by means of the procedure that is described in
    Section 4.4.7, “mysql_upgrade — Check and Upgrade MySQL Tables”.
    Attempting to redefine MySQL's system tables in any other fashion results in
    undefined (and unsupported!) behavior. The server is free to ignore rows that
    become malformed as a result of such modifications.

* 为了验证使用 MySQL 本地身份验证（由 `mysql_native_password` 验证插件实现）的客
  户端连接账户，服务器使用的是存储在 `user` 表里的密码。这些密码和你登录操作系统
  的密码是不同的。登录主机上 Windows 或 Unix 操作系统的密码和用于访问同一台主机
  上的 MySQL 服务器的密码没有必要的联系。

  如果服务器使用其他插件验证客户端，插件实现的验证方法可能是或者可能不是存储在
  `user` 表里的密码，这种情况下，使用外部密码用于服务器的身份验证是可能的。

* 存储在 `user` 表里的密码使用的是插件指定的算法加密的。关于 MySQL 本地密码的哈
  希信息，参见：Section 6.1.2.4, “Password Hashing in MySQL”

* 如果用户名和密码只包含 ASCII 字符，不管字符集的设置而直接连接到服务器是可能的
  。但是当连接时使用的用户名或密码包含非 ASCII 字符时，客户端应该使用
  `MYSQL_SET_CHARSET_NAME` 选项调用 C 语言的 API 函数 `mysql_options()` 并使用合
  适的字符集名作为参数，这样将使用指定的字符集进行身份验证。否则，身份验证将失败
  ，除非服务器的默认字符集和身份验证的默认值编码是一样的。

  标准的 MySQL 客户端程序支持一个选项 `--default-character-set`，可以促使 
  `mysql_options` 像上述描述的那样被调用，另外，客户端还支持字符集自动检测，参考
  ：Section 10.1.4, “Connection Character Sets and Collations”。

  对于使用了不是基于 C 语言 API 的连接器的程序，连接器应该提供了等价于
  `mysql_options` 的替代，请查阅连接器的文档。

  前面的说明不适用于 ucs2, utf16, utf32 字符集，因为不允许客户端使用这些字符集。

MySQL 安装进程使用一个初始化的 `root` 账户填充了授权表，参考：Section 2.10.4, “
Securing the Initial MySQL Accounts”，此节还描述了如何为其分配密码。

通常使用 `CREATE USER`, `DROP USER`, `GRANT`, `REVOKE` 等语句来设置、修改、删除
MySQL 账户，参考：Section 13.7.1, “Account Management Statements”.

要使用命令行客户端连接到 MySQL 服务器，请为您想要使用的账户指定用户名和密码选项：
```
shell> mysql --user=finley --password db_name
```
如果您喜欢短选项，那么命令如下
```
shell> mysql -u finley -p db_name
```

如刚才所示，如果您忽略了命令行上的 `--password` 或 `-p` 选项，那么客户端会互动的
提示您输入一个密码。另外，密码也可以在命令行上指定：
```
shell> mysql --user=finley --password=password db_name
shell> mysql -u finley -ppassword db_name
```
如果使用 `-p` 选项，那么在 `-p` 和后面的密码值之间必须没有空格。

直接在命令行行指定密码是不安全的，参考：Section 6.1.2.1,“End-User Guidelines
for Password Security”.

可以使用一个选项文件或一个登录路径文件来避免直接在命令行上使用密码，参考：
Section 4.2.6, “Using Option Files”, and Section 4.6.6,“mysql_config_editor
— MySQL Configuration Utility”.

关于用户名、密码和其他连接参数的额外信息，参考：Section 4.2.2, “Connecting to
the MySQL Server”

## 6.3.2 Adding User Accounts

可以用两种方式创建 MySQL 账户：

* 通过使用帐户管理语句来创建帐户并建立他们的特权，例如：`CREATE USER`,`GRANT`，
  这些语句使服务器对底层的授权表进行适当的修改。

* 通过直接使用诸如 `INSERT`、`UPDATE` 或 `DELETE` 之类的语句来直接操纵 MySQL 授
  权表。 

首选的方法是使用账户管理语句，因为相对于直接操作授权表，账户管理语句更简明和更不
容易犯错误。参考：Section 13.7.1,“Account Management Statements”

不鼓励直接操作授权表，如果因为此类修改而导致授权表的行畸形，则服务器会自动忽略这
些行。

创建账户的另一个选项是使用 GUI 工具 MySQL Workbench，另外，一些第三方程序也提供
了 MySQL 帐户管理的功能，例如 phpMyAdmin。

下面的例子展示了如何使用客户端程序 `mysql` 来建立新帐户。这些示例假设特权是根据
Section 2.10.4,“Securing the Initial MySQL Accounts”中描述的默认设置设置的，这
表示您已经以 `root` 用户的身份连接到 MySQL 服务器，因为 `root` 拥有 `CREATE
USER` 特权：

下面的例子使用 `CREATE USER` 和 `GRANT` 建立了 4 个账户：
```mysql
# 账户 1
mysql> CREATE USER 'finley'@'localhost' IDENTIFIED BY 'password';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'finley'@'localhost' WITH GRANT OPTION;

# 账户 2
mysql> CREATE USER 'finley'@'%' IDENTIFIED BY 'password';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'finley'@'%' WITH GRANT OPTION;

# 账户 3
mysql> CREATE USER 'admin'@'localhost' IDENTIFIED BY 'password';
mysql> GRANT RELOAD,PROCESS ON *.* TO 'admin'@'localhost';

# 账户 4
mysql> CREATE USER 'dummy'@'localhost';
```

使用上面语句创建的账户有下列属性：

* 账户1和账户2的用户名都是 `finley`，都是超级用户账户，有完全的权限来做任何事情。
  `'finley'@'localhost'` 账户只能从服务器所在的本地主机连接，`'finley'@'%'` 主机
  部分使用了通配符，因此可以从任何主机连接。

  如果主机 `localhost` 有匿名账户则 `'finley'@'localhost'` 是必要的，因为若没有
  此账户，`finley` 从本地主机连接时会被优先匹配为匿名用户账户，且最终被作为匿名
  用户对待。

  原因就是匿名用户账户的主机值 `'localhost'` 比账户 `'finley'@'%'` 的主机值更具
  体，因此在 `user` 表排序时更靠前。

* `'admin'@'localhost'` 账户只能被 `admin` 从本地主机连接。被授予了 `RELOAD` 和
  `PROCESS` 管理特权。这些特权使得用户 `admin` 可以执行 `mysqladmin
  reload`,`mysqladmin refresh`,`mysqladmin flush-xxx`,`mysqladmin processlist`这
  些命令。没有访问任何数据库的特权，你可以使用 `GRANT` 语句添加这些特权。

* `'dummy'@'localhost'` 账户没有密码（不安全不推荐）。此账户只能从本地主机登录。
    没有被授予任何特权。此账户被假定为稍后你会使用 `GRANT` 语句为其授予指定特权。

  要查看一个账户的特权，使用 `SHOW GRANTS` 语句：

    ```
    mysql> SHOW GRANTS FOR 'admin'@'localhost';
    +-----------------------------------------------------+
    | Grants for admin@localhost |
    +-----------------------------------------------------+
    | GRANT RELOAD, PROCESS ON *.* TO 'admin'@'localhost' |
    +-----------------------------------------------------+
    ```

  要查看一个账户的非特权属性，使用 `SHOW CREATE USER` 语句；

    ```
    mysql> SHOW CREATE USER 'admin'@'localhost'\G
    *************************** 1. row ***************************
    CREATE USER for admin@localhost: CREATE USER 'admin'@'localhost'
    IDENTIFIED WITH 'mysql_native_password'
    AS '*67ACDEBDAB923990001F0FFB017EB8ED41861105'
    REQUIRE NONE PASSWORD EXPIRE DEFAULT ACCOUNT UNLOCK
    ```

  下例中创建了 3 个账户，用户名都是 `custom`，密码都是 `password`，并授予他们指定数据库的权限：

    ```
    mysql> CREATE USER 'custom'@'localhost' IDENTIFIED BY 'password';
    mysql> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP
    -> ON bankaccount.*
    -> TO 'custom'@'localhost';

    mysql> CREATE USER 'custom'@'host47.example.com' IDENTIFIED BY 'password';
    mysql> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP
    -> ON expenses.*
    -> TO 'custom'@'host47.example.com';

    mysql> CREATE USER 'custom'@'%.example.com' IDENTIFIED BY 'password';
    mysql> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP
    -> ON customer.*
    -> TO 'custom'@'%.example.com';
    ```

这三个帐户可以如下使用：

* 第一个账户可以访问 `bankaccount` 数据库，但只能从服务端所在的本地主机访问。

* 第二个账户可以访问 `expense` 数据库，但只能从主机 `host host47.example.com` 访问。

* 第三个账户可以访问 `customer` 数据库，能从 `example.com` 域的任何主机访问。因
  为在账户名的主机值部分使用了 `%` 通配符所以有权从此域名的所有主机访问。

## 6.3.3 Removing User Accounts

移除账号使用 `DROP USER` 语句，例如：

```mysql
mysql> DROP USER 'jeffrey'@'localhost';
```

参见：Section 13.7.1.3, “DROP USER Syntax”

## 6.3.4 Reserved User Accounts

MySQL 安装进程的一部分工作就是数据目录的初始化（参考：Section 2.10.1.1, “
Initializing the Data Directory Manually Using mysqld”），在数据目录初始化期间
，MySQL 创建了保留的用户帐户：

* `'root'@'localhost'`：用于管理目的，此账户拥有所有特权且能执行任何操作。严格地
  说，这个帐户不是保留账户，因为有些安装会将 root 帐户重命名为别的东西，以避免使
  用一个众所周知的名称来暴露一个高度特权的帐户。

* `'mysql.sys'@'localhost'`，作为 `sys` 模式对象的 `DEFINER`。这个账户的使用避免
  了因为 DBA 重命名或移除 `root` 账户而发生问题。这个帐户是锁定的，所以它不能用
  于客户端连接

* `'mysql.session'@'localhost'`，由插件在内部用来访问服务器，这个帐户是锁定的，
  所以它不能用于客户端连接。

## 6.3.5 Setting Account Resource Limits

限制客户端使用 MySQL 服务器资源的一种方法是将全局 `max_user_connections` 系统变
量设置为非零值。这限制了任何给定账户可以同时连接的数量，但是对于客户端连接后可以
做什么没有做限制。此外，并不支持针对个别帐户设置 `max_user_connections`。这两种
类型（全局、个别）的控制都是 MySQL 管理员感兴趣的。

为了解决这些问题，MySQL 允许对使用下列服务器资源的个人帐户进行限制：
* 一个账户每小时可以发出的查询次数
* 一个账户每小时可以发出的更新次数
* 一个帐户可以每小时连接到服务器的次数
* 一个帐户并发连接到服务器的数量

客户端发出的任何查询都根据查询限制计数，
Any statement that a client can issue counts against the query limit, unless its results are served from the query cache. Only statements that modify databases or tables count against the update limit.

An “account” in this context corresponds to a row in the mysql.user table. That is, a connection is assessed against the User and Host values in the user table row that applies to the connection. For example, an account 'usera'@'%.example.com' corresponds to a row in the user table that has User and Host values of usera and %.example.com, to permit usera to connect from any host in the example.com domain. In this case, the server applies resource limits in this row collectively to all connections by usera from any host in the example.com domain because all such connections use the same account.

Before MySQL 5.0.3, an “account” was assessed against the actual host from which a user connects.  This older method of accounting may be selected by starting the server with the --old-style- user-limits option. In this case, if usera connects simultaneously from host1.example.com and host2.example.com, the server applies the account resource limits separately to each connection.  If usera connects again from host1.example.com, the server applies the limits for that connection together with the existing connection from that host.

To establish resource limits for an account at account-creation time, use the CREATE USER statement.  To modify the limits for an existing account, use ALTER USER. (Before MySQL 5.7.6, use GRANT, for new or existing accounts.) Provide a WITH clause that names each resource to be limited. The default value for each limit is zero (no limit). For example, to create a new account that can access the customer database, but only in a limited fashion, issue these statements: 
```
mysql> CREATE USER 'francis'@'localhost' IDENTIFIED BY 'frank'
-> WITH MAX_QUERIES_PER_HOUR 20
-> MAX_UPDATES_PER_HOUR 10
-> MAX_CONNECTIONS_PER_HOUR 5
-> MAX_USER_CONNECTIONS 2;
```

The limit types need not all be named in the WITH clause, but those named can be present in any order. The value for each per-hour limit should be an integer representing a count per hour. For MAX_USER_CONNECTIONS, the limit is an integer representing the maximum number of simultaneous connections by the account. If this limit is set to zero, the global max_user_connections system variable value determines the number of simultaneous connections. If max_user_connections is also zero, there is no limit for the account.

To modify limits for an existing account, use an ALTER USER statement. The following statement changes the query limit for francis to 100:

```
mysql> ALTER USER 'francis'@'localhost' WITH MAX_QUERIES_PER_HOUR 100;
```

The statement modifies only the limit value specified and leaves the account otherwise unchanged.

To remove a limit, set its value to zero. For example, to remove the limit on how many times per hour francis can connect, use this statement:

```
mysql> ALTER USER 'francis'@'localhost' WITH MAX_CONNECTIONS_PER_HOUR 0;
```

As mentioned previously, the simultaneous-connection limit for an account is determined from the MAX_USER_CONNECTIONS limit and the max_user_connections system variable. Suppose that the global max_user_connections value is 10 and three accounts have individual resource limits specified as follows:

```
ALTER USER 'user1'@'localhost' WITH MAX_USER_CONNECTIONS 0;
ALTER USER 'user2'@'localhost' WITH MAX_USER_CONNECTIONS 5;
ALTER USER 'user3'@'localhost' WITH MAX_USER_CONNECTIONS 20;
```

user1 has a connection limit of 10 (the global max_user_connections value) because it has a MAX_USER_CONNECTIONS limit of zero. user2 and user3 have connection limits of 5 and 20, respectively, because they have nonzero MAX_USER_CONNECTIONS limits.  

The server stores resource limits for an account in the user table row corresponding to the account. The max_questions, max_updates, and max_connections columns store the per-hour limits, and the max_user_connections column stores the MAX_USER_CONNECTIONS limit. (See Section 6.2.2, “Grant Tables”.)

Resource-use counting takes place when any account has a nonzero limit placed on its use of any of the resources.

As the server runs, it counts the number of times each account uses resources. If an account reaches its limit on number of connections within the last hour, the server rejects further connections for the account until that hour is up. Similarly, if the account reaches its limit on the number of queries or updates, the server rejects further queries or updates until the hour is up. In all such cases, the server issues appropriate error messages.

Resource counting occurs per account, not per client. For example, if your account has a query limit of 50, you cannot increase your limit to 100 by making two simultaneous client connections to the server. Queries issued on both connections are counted together.

The current per-hour resource-use counts can be reset globally for all accounts, or individually for a given account:

• To reset the current counts to zero for all accounts, issue a FLUSH USER_RESOURCES statement.  The counts also can be reset by reloading the grant tables (for example, with a FLUSH PRIVILEGES statement or a mysqladmin reload command).

• The counts for an individual account can be reset to zero by setting any of its limits again. Specify a limit value equal to the value currently assigned to the account.

Per-hour counter resets do not affect the MAX_USER_CONNECTIONS limit.

All counts begin at zero when the server starts. Counts do not carry over through server restarts.

For the MAX_USER_CONNECTIONS limit, an edge case can occur if the account currently has open the maximum number of connections permitted to it: A disconnect followed quickly by a connect can result in an error (ER_TOO_MANY_USER_CONNECTIONS or ER_USER_LIMIT_REACHED) if the server has not fully processed the disconnect by the time the connect occurs. When the server finishes disconnect processing, another connection will once more be permitted.

## 6.3.6 Assigning Account Passwords



## 6.3.7 Password Management



## 6.3.8 Password Expiration and Sandbox Mode



## 6.3.9 Pluggable Authentication



## 6.3.10 Proxy Users



## 6.3.11 User Account Locking



## 6.3.12 SQL-Based MySQL Account Activity Auditing


