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

客户端发出的任何查询都进行计数并和查询次数限制进行比较，除非结果直接取自查询缓存
。但只有修改了数据库或表的语句才和更新次数限制比较。

在此上下文中的“帐户”对应于 `mysql.user` 中的一行。也就是说，一个连接是根据应用
于此连接的 `user` 表中的 `User` 和 `Host` 值进行评估的。例如，一个账户
`'usera'@'example.com'` 对应于 `user` 表中的一行，此行的 `User` 和 `Host` 值分别
为 `usera` 和 `%.example.com`，允许 `usera` 从 `example.com` 域的任何主机连接。
在这种情况下，服务器把这一行的资源限制集体应用到用户 `usera` 从域 `example.com`
中的任何一台主机发起的所有连接，因为所有这些连接都使用同一个帐户。

在 MySQL 5.0.3 之前，“账户”是根据用户连接自的实际主机进行评估的，这种较老的方
法可以通过在服务器启动时使用 `--old-style-user-limits` 选项来选择。在这种情况下
，如果 `usera` 同时从 `host1.example.com` 和 `host2.example.com` 连接，服务器将
分别为两个连接应用账户资源限制。如果 `usera` 再次从 `host1.example.com` 连接，服
务器将与来自此主机的现有连接一起应用该连接的限制。

在账户建立阶段为账户设置资源限制，可以使用 `CREATER USER` 语句的 `WITH` 子句，修
改已存在账户的资源限制，使用 `ALTER USER` 语句（在 MySQL 5.7.6 之前，使用
`GRANT` 语句，无论新账户还是已存在的账户）。


例如，创建一个可以访问 `customer` 数据库的新账户，但是以限制的方式：
```
mysql> CREATE USER 'francis'@'localhost' IDENTIFIED BY 'frank'
-> WITH MAX_QUERIES_PER_HOUR 20
-> MAX_UPDATES_PER_HOUR 10
-> MAX_CONNECTIONS_PER_HOUR 5
-> MAX_USER_CONNECTIONS 2;
```
* 每种资源限制的默认值是 0，表示无限制。
* 限制类型不必全部列在 `WITH` 子句里
* 列出的限制类型没有顺序要求。
* 用于每小时计数(_PER_HOUR)的限制值应该是一个整数。
* `MAX_USER_CONNECTIONS` 也是一个整数，表示账户的最大并发连接数。如果此值被设为
  0，则系统变量 `max_user_connections` 的值确定了最大并发连接数，如果后者也被设
  为 0，则账户的最大并发连接数没有限制。

要修改已存在账户的限制，使用 `ALTER USER` 语句，例如，下面的语句把用户 `francis`
的查询限制设为了 100：
```
mysql> ALTER USER 'francis'@'localhost' WITH MAX_QUERIES_PER_HOUR 100;
```
* 语句值修改了指定的限制，其他的没有改变


移除一个限制，可以设置它的值为 0。例如，移除用户 `francis` 一个小时内可以连接的
次数限制，使用语句：
```
mysql> ALTER USER 'francis'@'localhost' WITH MAX_CONNECTIONS_PER_HOUR 0;
```

就像上面提到的，一个账户的并发连接数限制是由 `MAX_USER_CONNECTIONS` 限制和系统变
量 `max_user_connections` 确定的，假设后者的值是 10，下面三个账户分别设置了单独
的资源限制：
```
ALTER USER 'user1'@'localhost' WITH MAX_USER_CONNECTIONS 0;
ALTER USER 'user2'@'localhost' WITH MAX_USER_CONNECTIONS 5;
ALTER USER 'user3'@'localhost' WITH MAX_USER_CONNECTIONS 20;
```
则最终各用户的最大并发连接数限制值为： user1 为 10，user2 为 5，user3 为 20。

服务器在 `user` 表中与账户对于的行里存储了一个账户的资源限制信息。`max_questions`,`max_updates`,`max_connections` 列存储了每小时的限制，`max_user_connections` 存储了 `MAX_USER_CONNECTIONS` 限制。参考：Section 6.2.2, “Grant Tables”

当任何帐户使用的任何资源是非零限制时，就会发生资源使用计数。

服务器运行时会计数每个账户使用资源的次数，如果一个账户在最后一个小时内达到了它的
连接限制数，服务器将拒绝此账户进一步的连接直到那个小时结束为止。查询和更新的限制
和连接相同。在所有这些情况下，服务器都会发出适当的错误信息。

资源计数是基于账户的，不是基于客户端的。例如，如果你的账户的查询限制是 50，你不
能通过同时向服务器发起两个并发的客户端连接把限制增加到 100。两个连接发出的查询是
一起计数的。

全局的所有账户或某一个给定账户的当前每小时资源使用计数都可以重置：

* 要把所有账户的当前计数重置为 0，执行 `FLUSH USER_RESOURCES` 语句。计数还可以通
  过重载授权表来实现，例如通过 `FLUSH PRIVILEGES` 语句或 `mysqladmin reload` 命令。

* 个人账户计数可以通过
  The counts for an individual account can be reset to zero by setting any of
  its limits again. Specify a limit value equal to the value currently assigned
  to the account.

每小时计数器的重置不影响 `MAX_USER_CONNECTIONS` 的限制。

服务器启动时，所有计数从 0 开始。计数不会通过服务器重启后继续存在。
Counts do not carry over through server restarts.

对于 `MAX_USER_CONNECTIONS` 限制，如果当前账户打开了允许的最大连接数，则一种极端
情况可能发生：
For the MAX_USER_CONNECTIONS limit, an edge case can occur if the account
currently has open the maximum number of connections permitted to it:

如果服务器在连接发生时没有完全处理完断开的连接，那么一个连接之后快速地断开连接可
能导致错误（`ER_TOO_MANY_USER_CONNECTIONS` 或者 `ER_USER_LIMIT_REACHED`）。当服
务器完成断开处理时，另一个连接将再次被允许。
A disconnect followed quickly by a connect can result in an error
(ER_TOO_MANY_USER_CONNECTIONS or ER_USER_LIMIT_REACHED) if the server has not
fully processed the disconnect by the time the connect occurs. When the server
finishes disconnect processing, another connection will once more be permitted.

## 6.3.6 Assigning Account Passwords

连接到 MySQL 服务器的客户端所需的凭证可以包含一个密码。本节描述如何为 MySQL 帐户
分配密码。

MySQL 在系统数据库 `mysql` 的 `user` 表里存储了凭证。只有拥有 `CREATE USER` 特权
的或者拥有 `mysql` 数据库特权（`INSERT` 特权用于创建新账户，`UPDATE` 特权用于修
改已存在账户）的用户才能分配或修改密码，。如果启用了 `read_only` 系统变量，则使
用账户修改语句如 `CREATE USER` 或 `ALTER USER` 还需要 `SUPER` 特权。

这里的讨论只总结了最常见的密码赋值语句的语法。关于其他可能性的完整的详细信息参考：
Section 13.7.1.2, “CREATE USER Syntax”
Section 13.7.1.1, “ALTER USER Syntax”
Section 13.7.1.4, “GRANT Syntax”
Section 13.7.1.7, “SET PASSWORD Syntax”.

MySQL 使用插件执行客户端身份验证，参考：Section 6.3.9, “Pluggable
Authentication”

在密码分配语句中，与账户相关联的认证插件执行指定的明文密码所需的任何散列。这使得
MySQL可以在将密码存储到 `mysql.user` 之前将其混淆。

对于这里描述的语句，MySQL 会自动地将指定的密码散列。还为 `CREATER USER` 和
`ALTER USER` 提供了语法，允许在字面上指定散列值(permit hashed values to be
specified literally)。有关详细信息，请参见对这些语句的描述。
 
创建新账户时为其赋予密码，使用 `CREATER USER` 语句并包含 `IDENTIFIED BY` 子句：
```
CREATE USER 'jeffrey'@'localhost' IDENTIFIED BY 'password';
```
`CREATE USER` 还支持指定账户验证插件的语法，参考：Section 13.7.1.2,“CREATE USER
Syntax”.

对于一个已存在的账户，为其分配密码或修改密码使用 `ALTER USER` 并包含 `IDENTIFIED
BY` 子句:
```
ALTER USER 'jeffrey'@'localhost' IDENTIFIED BY 'password';
```

如果你**不**是以匿名用户连接的，修改自己的密码时可以不写出自己的账户名：
```
ALTER USER USER() IDENTIFIED BY 'password';
```

从命令行也可以修改账户密码，使用 `mysqladmin` 命令：
```
mysqladmin -u user_name -h host_name password "password"
```

    警告：

    通过 `mysqladmin` 设置密码应被认为是不安全的。在一些儿系统中，你的密码对于如
    `ps` 一类的检测系统状态的程序是可见的，而此类程序可能被其他用户调用并显示在
    命令行上。

    MySQL 客户端通常在初始化序列中用多个 0 覆盖命令行密码参数。然而，仍然有一个
    短暂的间隔，在此期间值是可见的。而且，在某些系统上，这种重写策略是无效的，并
    且密码对 `ps` 来说仍然是可见的（SystemV Unix系统，或者其他系统可能会受到这个
    问题的影响）。

如果您使用的是 MySQL 复制，请注意，当前，复制从服务器使用的密码作为 `CHANGE
MASTER TO` 语句的一部分，实际上长度被限制为 32 个字符;如果密码再长点儿，任何多余
字符都被截断。这不是由于 MySQL 服务器通常所施加的限制，而是一个特定于 MySQL 复制
的问题。（关于更多信息，请参见 Bug #43439.）

## 6.3.7 Password Management

MySQL允许数据库管理员手动终止/过期帐户密码，并建立密码自动过期的策略。在全局范围
内或在每个账户上都可以建立过期政策。这些功能适用于使用 MySQL 内置身份验证插件（
`mysql_native_password`或`sha256_password`）的账户。对于使用基于外部凭证系统执行
身份验证的插件的帐户，密码过期也必须在外部处理。

要手动过期账户密码，使用 `ALTER USER` 语句：
```
ALTER USER 'jeffrey'@'localhost' PASSWORD EXPIRE;
```
此操作标记 `mysql.user` 表里对应的行密码过期。

根据策略，密码过期是自动的和基于密码年龄的，对于给定的账户，密码过期根据最近的密
码更改时间进行评估。`mysql.user` 表表明了每个账户最后更改密码的时间。客户端连接
时如果密码年龄大于它被允许的生命期则被服务器判断为过期，是不需要显式的手动密码过
期的。

建立全局的密码自动过期策略，可以使用系统变量 `default_password_lifetime`，默认值
是 0，表示禁用此策略。如果值是正整数 N，则表明了允许的密码生命期，表示密码必须每
N 天修改一次。

    Note
    注意：

    在 5.7.11 之前，`default_password_lifetime` 默认值是 360（即密码大约每年改一
    次），对于这些版本，需要注意的是，如果没有改变 `default_password_lifetime`
    的值或者没有修改过个人用户账户，则在 360 天每个用户密码过期后，账户开始在受
    限模式下运行。使用这些账户登录的客户端会收到错误信息表明必须修改密码：ERROR
    1820 (HY000): You must reset your password using ALTER USER statement before
    executing this statement.

    然而，对于那些自动连接到服务器的客户端来说，这很容易被忽略，比如由脚本生成的
    连接。为了避免因为密码过期导致这些客户端突然停止工作，请确保为这些客户端更改
    密码过期设置，如下所：

    ```
    ALTER USER 'script'@'localhost' PASSWORD EXPIRE NEVER
    ```

    或者，将 `default_password_lifetime` 变量设置为 0，从而禁用所有用户的自动密
    码过期。

例子：

* 建立一个全局策略：密码有大约 6 个月的生命期，可以在 `my.cnf` 文件里加入下面行：

    ```
    [mysqld]
    default_password_lifetime=180
    ```

* 建立一个全局策略：密码永不过期：

    ```
    [mysqld]
    default_password_lifetime=0
    ```

* 也可在运行时改变密码的生命期：

    ```
    SET GLOBAL default_password_lifetime = 180;
    SET GLOBAL default_password_lifetime = 0;
    ```

为个别账户建立密码过期策略，在 `CREATER USER` 和 `ALTER USER` 语句里使用
`PASSWORD EXPIRE` 选项，参考：
Section 13.7.1.2, “CREATE USER Syntax”
Section 13.7.1.1, “ALTER USER Syntax”

指定账户过期举例：

要求密码每 90 天更改一次
```
CREATE USER 'jeffrey'@'localhost' PASSWORD EXPIRE INTERVAL 90 DAY;
ALTER USER 'jeffrey'@'localhost' PASSWORD EXPIRE INTERVAL 90 DAY;
```

禁用密码过期
```
CREATE USER 'jeffrey'@'localhost' PASSWORD EXPIRE NEVER;
ALTER USER 'jeffrey'@'localhost' PASSWORD EXPIRE NEVER;
```

遵守全局过期策略
```
CREATE USER 'jeffrey'@'localhost' PASSWORD EXPIRE DEFAULT;
ALTER USER 'jeffrey'@'localhost' PASSWORD EXPIRE DEFAULT;
```

客户端成功连接后，服务器确定账户密码是否已过期：
* 服务器检查密码是否已手动过期
* 否则，服务器将根据自动密码过期策略检查密码年龄是否大于其允许的生命周期。如果是
  这样，服务器认为密码过期了。

如果密码过期了（无论是手动还是自动），服务器将断开客户端的连接或者限制客户端的操
作（参考：Section 6.3.8, “Password Expiration and Sandbox Mode”），受限制的客
户端执行操作时会出现错误提示，直到创建一个新的密码。
```
mysql> SELECT 1;
ERROR 1820 (HY000): You must reset your password using ALTER USER statement
before executing this statement.

mysql> ALTER USER USER() IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)
mysql> SELECT 1;
+---+
| 1 |
+---+
| 1 |
+---+
1 row in set (0.00 sec)
```

This restricted mode of operation permits SET statements, which is useful before
MySQL 5.7.6 if SET PASSWORD must be used instead of ALTER USER and the account
password has a hashing format that requires old_passwords to be set to a value
different from its default.
这种受限制的操作模式允许 `SET` 语句，在 MySQL 5.6.6 之前是有用的，如果 `SET
PASSWORD` 必须使用，而不是 `ALTER USER`，账户密码有一个哈希格式，需要将
`old_password` 设置为与默认值不同的值。

After the client resets the password, the server restores normal access for the
session, as well as for subsequent connections that use the account. It is also
possible for an administrative user to reset the account password, but any
existing restricted sessions for that account remain restricted. A client using
the account must disconnect and reconnect before statements can be executed
successfully.
当客户端重新设置密码之后，服务器会恢复会话的正常访问，以及使用该帐户的后续连接。
具有管理权限的用户也可以重新设置此账户密码，但是该帐户的任何现有限制会话仍然受到
限制。使用该帐户的客户端必须断开连接并重新连接，才能成功执行语句。

    注意：
    通过将密码设置为当前值“重置”密码是可能的。但更好的策略是选择一个不同的密码。


## 6.3.8 Password Expiration and Sandbox Mode

对于使用过期密码账户的连接，服务器要么断开连接客户端，要么将客户端限制为“沙盒模
式”，在这种模式下，服务器只允许客户端进行重置过期密码所必须的操作。服务器所采取
的操作取决于客户端和服务器设置，稍后后面讨论。

如果服务器断开客户端，则返回 `ER_MUST_CHANGE_PASSWORD_LOGIN` 错误：
```
shell> mysql -u myuser -p
Password: ******
ERROR 1862 (HY000): Your password has expired. To log in you must
change it using a client that supports expired passwords.
```

如果服务器将客户端限制为沙盒模式，客户端会话中允许这些操作：

* 客户端可以使用 `ALTER USER` 或 `SET PASSWORD` 重置账户密码，密码重置后，服务器
  恢复会话的正常访问，包括此账户的后续连接。

  将密码“重置”为当前密码值是可能的，但更好的选择是使用另一个不同的密码。

* 在 MySQL 5.7.6 之前如果必须使用 `SET PASSWORD` 代替 `ALTER USER`，客户端可以使
  用 `SET` 语句，账户密码必须是一个散列格式，将 `old_passwords` 设置为不同于其默
  认值的值。

对于在会话中不被允许的任何操作，服务器返回 `ER_MUST_CHANGE_PASSWORD` 错误：
```
mysql> USE performance_schema;
ERROR 1820 (HY000): You must reset your password using ALTER USER
statement before executing this statement.

mysql> SELECT 1;
ERROR 1820 (HY000): You must reset your password using ALTER USER
statement before executing this statement.
```
这就是 `mysql` 客户端的交互式调用通常会发生的情况，因为默认情况下，这样的调用会
被放在沙盒模式中。要清除错误并恢复正常功能，请选择一个新密码。

对于 `mysql` 客户端的非交互式调用（例如在批处理模式中），如果密码过期则服务器通
常会断开连接。为了允许非交互式 `mysql` 调用保持连接，以便可以更改密码，可以使用
`mysql` 命令的 `--connect-expired-password` 选项。

正如前面提到的，服务器是否断开连接一个密码到期的客户端，或者将其限制为沙箱模式，
取决于客户端和服务器设置的组合。下面的讨论描述了相关的设置以及它们之间的交互方式
。这个讨论只适用于密码过期的账户。如果客户端连接密码没有过期，服务器会按正常情
况处理客户端。

On the client side, a given client indicates whether it can handle sandbox mode for expired passwords. For clients that use the C client library, there are two ways to do this:
在客户端，一个给定的客户端表明它是否能够处理过期密码的沙盒模式。对于使用 C 客户端库的客户端，有两种方法可以做到这一点：

* 在连接之前，把 `MYSQL_OPT_CAN_HANDLE_EXPIRED_PASSWORDS` 标记传递给 `mysql_options()`：

```
arg = 1;
result = mysql_options(mysql, MYSQL_OPT_CAN_HANDLE_EXPIRED_PASSWORDS, &arg);
```

如果交互的调用 `mysql` 客户端或传递了 `--connect-expired-password` 选项，则
`mysql` 客户端启用了 `MYSQL_OPT_CAN_HANDLE_EXPIRED_PASSWORDS`

* 在连接时把标记 `CLIENT_CAN_HANDLE_EXPIRED_PASSWORDS` 传递给 `mysql_real_connect()`：

```
mysql = mysql_real_connect(mysql,
host, user, password, db,
port, unix_socket,
CLIENT_CAN_HANDLE_EXPIRED_PASSWORDS);
```

其他 MySQL 连接器有它们自己的约定来表示准备处理沙盒模式。请参阅您感兴趣的连接器
的文档。

在服务器端，如果客户端表明它可以处理过期的密码，服务器将其放入沙盒模式。

如果客户端没有表明它可以处理过期的密码（或者使用了一个旧版本的客户机库不能表明）
，则服务器的操作将依赖于系统变量 `disconnect_on_expired_password` 的值：

* 如果启用了 `disconnect_on_expired_password` (缺省值)，服务器断开客户端连接提示
  `ER_MUST_CHANGE_PASSWORD_LOGIN` 错误。

* 如果禁用了 `disconnect_on_expired_password`，服务器端会把客户端放入沙盒模式。

## 6.3.9 Pluggable Authentication

客户端连接到 MySQL 服务器时，服务器根据客户端提供的用户名和客户端主机去
`mysql.user` 表里选择合适的行。服务器接着验证客户端，从账户对应的行确定使用哪个
验证插件来验证客户端。服务器调用插件验证用户后，插件为服务器返回一个状态表明是否
允许用户连接。如果服务器无法找到对应的插件，则发生错误并拒绝连接。

插件式的身份验证支持两个重要功能：

* 外部身份验证：插件式的身份验证使得客户端使用凭证连接到服务器是可能的。存储凭证
  到合适的地方而不是在 `mysql.user` 表里的身份验证方法和凭证相适应。例如可以创建
  插件来使用诸如 PAM、Windows 登录 ID、LDAP 或 Kerberos 等外部认证方法
  Pluggable authentication makes it possible for clients to connect to the MySQL
  server with credentials that are appropriate for authentication methods that
  store credentials elsewhere than in the mysql.user table. 

* 代理用户：如果一个用户允许连接，验证插件可以为服务器返回一个不同于连接用户的用
  户名，这表明了正在连接的用户是另一个用户的代理用户。当连接持续时，为了访问控制
  目的，代理用户拥有的是被代理用户的特权。实际上就是一个用户模仿了另一个用户。
  更多信息参考：Section 6.3.10, “Proxy Users”.

MySQL 中几种可用的身份验证插件：

* 执行本地认证（native authentication 原生验证、本机身份验证）的插件：也就是说，
  在引入插件式身份验证之前， MySQL 中使用的是基于密码散列方法的身份验证。
  `mysql_native_password` 插件基于本机密码散列方法实现身份验证。
  `mysql_old_password` 插件基于旧的密码散列方法（pre-4.1，在MySQL 5.7.5中被弃用
  和删除）实现本机身份验证。
  参考：
  Section 6.5.1.1, “Native Pluggable Authentication”
  Section 6.5.1.2, “Old Native Pluggable Authentication”.

* 一个使用 `SHA-256` 密码哈希进行认证的插件。这种加密比本地认证更强大。
  参考 Section 6.5.1.4, “SHA-256 Pluggable Authentication”.

* 一个客户端插件，它可以将密码发送到服务器，而无需进行散列或加密。这个插件与服务
  器端插件一起使用，服务端插件需要像客户端用户那样提供同样的访问密码。
  参考：Section 6.5.1.5, “Client-Side Cleartext Pluggable Authentication”.

* 一个插件使用 PAM (Pluggable Authentication Modules 可插入的认证模式) 执行外部
  的认证，使的 MySQL 服务器可以使用 PAM 对 MySQL 用户进行身份验证。这个插件也支
  持代理用户。
  参考： Section 6.5.1.6, “PAM Pluggable Authentication”.

* Windows 上一个执行外部验证的插件，使得 MySQL 服务器可以使用本地的 Windows 服务
  来验证客户端连接。登录到 Windows 系统的用户可以根据他环境中的信息通过 MySQL 客
  户端程序连接到服务器，而无需额外指定密码。此插件也支持代理用户。
  参考：Section 6.5.1.7,“Windows Pluggable Authentication”.

* 使用 LDAP (Lightweight Directory Access Protocol 轻量级目录访问协议) 执行身份
  验证的多个插件，通过诸如 X.500 这样的访问目录服务进行验证。这些插件都支持代理
  用户参考：Section 6.5.1.8, “LDAP Pluggable Authentication”.

* 一个插件可以防止所有的客户端连接到任何使用此插件的账户。这个插件的用例包括：
    * 必须能够执行具有较高权限的存储程序和视图的账户，而不需要将这些特权暴露给普
      通用户
    * 那些不应该允许直接登录，而只能通过代理帐户访问的被代理的账户。
  参考：Section 6.5.1.9, “No-Login Pluggable Authentication”.

* 一个插件，对从本地主机通过 Unix 套接字文件连接的客户端进行身份验证。
  参考：Section 6.5.1.10, “Socket Peer-Credential Pluggable Authentication”.

* 一个测试插件，它检查帐户凭证，并将成功或失败记录到服务器错误日志。这个插件是用
  于测试和开发的目的，也是一个如何编写认证插件的例子。
  参考：Section 6.5.1.11, “Test Pluggable Authentication”.

    注意：
    插件式身份验证使用时目前的限制信息，包括哪些连接器支持哪些插件，参考：Section C.9, “Restrictions on Pluggable Authentication”.
    第三方连接器开发人员应该阅读这一节，以确定连接器能够利用可插入身份验证功能的程度，以及采取哪些步骤使其更加兼容。

如果有兴趣自己编写插件, 参考：Section 28.2.4.9, “Writing Authentication Plugins”.

### 身份验证插件使用说明

本节提供了安装和使用身份验证插件的一般说明。对于特定插件的说明，请参阅描述该插件
的部分。

一般来说，插件式身份验证在服务器和客户端同时使用相应的插件，因此您可以使用下面列
出的认证方法：

* 如果有必要，安装插件库或包含适当插件的库。在服务器主机上，安装包含服务器端插件
  的库，这样服务器就可以使用它来验证客户端连接。类似地，在每个客户端主机上，安装
  包含客户端插件的库，供客户端程序使用。嵌入式认证插件不需要安装。Authentication
  plugins that are built in need not be installed.

* 对于你创建的每个 MySQL 帐户，为其指定合适的服务器端插件来进行认证。

* 当客户端连接时，服务器端插件会告诉客户端程序，哪个客户端插件可以用于身份验证。

  如果一个账户使用的身份验证方法就是服务器端和客户端程序共同的默认方法，那么服务
  器就不需要和客户端沟通客户端使用的是哪个插件，并且可以避免在客户机/服务器协商
  中进行往返。对于使用本机 MySQL 身份验证（`mysql_native_password` 或
  `mysql_old_password`）的帐户来说，这是正确的。

  `default-auth=pluginname` 选项可以在 `mysql` 命令行中指定，作为程序可以期望使
  用的客户端插件的提示，尽管如果与用户帐户相关联的服务器端插件需要一个不同的客户
  端插件，那么服务器将会覆盖掉它。

  如果客户端程序没有找到客户端插件，那么指定一个 `--plugin-dir=dirname` 选项来指示插件的位置。

    注意：
    如果您使用 `--skip-grant-tables` 选项启动服务器，即使装载了身份验证插件，也
    不会使用身份验证插件，因为服务器不执行客户端身份验证，并允许任何客户端进行连
    接。因为这是不安全的，所以您可能想要使用 '-skip-grant-tables' 配合
    '-skip-networking' 来防止远程客户端连接。

## 6.3.10 Proxy Users

MySQL 服务器使用身份验证插件对客户端连接进行身份验证。对给定连接进行身份验证的插
件可能要求将连接（外部）用户视为不同的用户进行特权检查。这使得外部用户可以成为第
二个用户的代理;也就是说，承担第二个用户的特权：
* 外部用户是一个“代理用户”（一个可以模拟或被称为另一个用户的用户）。
* 第二个用户是一个“被代理用户”（用户的身份和特权可以由代理用户承担）。

本节描述代理用户功能是如何工作的。
关于认证插件的一般信息，参考：Section 6.3.9, “Pluggable Authentication”.
关于特定插件的信息，参考：Section 6.5.1, “Authentication Plugins”.
有关编写支持代理用户的身份验证插件的信息，参考：Section 28.2.4.9  Writing
Authentication Plugins - Implementing Proxy User Support in Authentication
Plugins.

### Requirements for Proxy User Support

对于一个给定的认证插件来说，代理必须满足以下条件：

* 代理必须由插件本身或代表插件的 MySQL 服务器支持。在后一种情况下，可能需要显式
  地启用服务器支持。参见：Server Support for Proxy User Mapping.

* 代理用户帐户必须设置为经由插件验证。使用 `CREATE USER` 语句将账户与身份验证插
  件关联起来，或者使用 `ALTER USER` 来更改其插件。

* 必须创建被代理的用户账户，且必须授权给被代理的用户账户特权，这些特权将由代理用
  户承担。（使用 `CREATE USER` 和 `GRANT`）

* 代理用户账户必须有被代理账户的 `PROXY` 特权，使用 `GRANT` 语句。

* 连接到代理账户的客户端被作为代理用户看待，验证插件必须返回一个和客户端用户名不
  同的用户名，以表明被代理账户的用户名，被代理的账户定义了代理账户承担的特权。

  或者，对于由服务器提供代理映射的插件，被代理用户由代理用户持有的 `PROXY` 特权
  决定。

代理机制只允许映射客户端用户名到被代理的用户名。没有可以映射主机名的条款。当一个
连接的客户端匹配到一个代理账户，服务器就尝试使用验证插件返回的用户名和代理账户的
主机名来为被代理的账户找到一个匹配。

考虑以下帐户定义：
```
-- create proxy account
CREATE USER 'employee_ext'@'localhost'
IDENTIFIED WITH my_auth_plugin AS 'my_auth_string';
```
* `IDENTIFIED WITH` 子句命名了验证插件。
* 后面跟着的 `AS 'auth_string'` 子句指定了用户连接时服务器传递给插件的字符串。
* `AS 'auth_string'` 是可选的，如果出现了，则此字符串提供了信息帮助插件确定怎么
  映射外部客户端用户名到一个被代理的用户名。
* 是否需要 `AS` 子句取决于每个插件，如果需要，验证字符串的格式依赖于插件打算怎么
  使用它。对于一个给定的插件，可以查阅它的文档来确定它接受的验证字符串的值。

当客户端从本地主机以用户名 `employee_ext` 连接时，MySQL 使用名为
`my_auth_plugin` 的插件来执行身份验证。假设 `my_auth_plugin` 根据
`my_auth_string` 的内容并可能通过查阅一些外部认证系统返回给服务器一个用户名
`employee`，和 `employee_ext` 不同，因此返回的 `employee` 作为对服务器的一个请求
：以权限检查为目的时，把 `employee_ext` 客户端当作本地用户 `employee` 来对待。

这种情况下，`employee_ext` 是代理用户，`employeet` 是被代理的用户。

服务器为用户 `employee_ext` 核实 `employee` 的代理验证是可能的，通过检测
`employee_ext`（代理用户） 是否有 `employee`（被代理用户） 的 `PROXY` 特权。

```
-- grant PROXY privilege to proxy account for proxied account
GRANT PROXY
ON 'employee'@'localhost'
TO 'employee_ext'@'localhost';

-- create proxied account and grant its privileges
CREATE USER 'employee'@'localhost'
IDENTIFIED BY 'employee_pass';
GRANT ALL ON employees.*
TO 'employee'@'localhost';
```
如果没有被授予 `PROXY` 特权，就会发生错误。否则，`employee_ext` 承担 `employee`
的特权。在客户端会话期间，服务器会核对被 `employee_ext` 执行的语句和被授予
`employee` 的特权。这个例子中，`employee_ext` 可以访问 `employees` 数据库中的表
。

进行代理时，`USER()` 和 `CURRENT_USER()` 函数可以用来查看连接用户（代理用户）
和在当前会话期间应用特权的帐户（被代理用户）之间的区别。对于刚刚描述的例子，这些
函数返回这些值：
```
mysql> SELECT USER(), CURRENT_USER();
+------------------------+--------------------+
| USER()                 | CURRENT_USER()     |
+------------------------+--------------------+
| employee_ext@localhost | employee@localhost |
+------------------------+--------------------+
```

### Granting the Proxy Privilege

使得一个外部用户以另一个用户的身份连接和拥有另一个用户的特权，需要使用 `GRANT`
语句授予 `PROXY` 特权，例如：
```
GRANT PROXY ON 'proxied_user' TO 'proxy_user';
```
* 这条语句在授权表 `mysql.proxies_priv` 里插入了一行。
* 在连接时，`proxy_user` 必须代表一个有效的外部认证的 MySQL 用户，而
  `proxied_user` 必须代表有效的本地身份验证用户。否则，连接尝试失败。

对应的撤销语法 `REVOKE`：
```
REVOKE PROXY ON 'proxied_user' FROM 'proxy_user';
```

MySQL 的 `GRANT` 和 `REVOKE` 语法扩展：
```
GRANT PROXY ON 'a' TO 'b', 'c', 'd';
GRANT PROXY ON 'a' TO 'd' WITH GRANT OPTION;
GRANT PROXY ON 'a' TO ''@'';
REVOKE PROXY ON 'a' FROM 'b', 'c', 'd';
```

`PROXY` 特权可以在下列情况下被授权：
* 一个有被代理用户 `proxied_user` 的 `GRANT PROXY ... WITH GRANT OPTION` 特权的
  用户
* 被代理用户 `proxied_user` 自己：`USER()` 的值必须准确匹配 `CURRENT_USER()` 和
  `proxied_user`，包括账户名里的用户名和主机名部分都是。

在 MySQL 安装过程中，最初的 `root` 账户被创建，他拥有 `''@''` 的 `PROXY ... WITH
GRANT OPTION` 特权，就是说，所有用户和所有主机。这使得 `root` 可以建立代理用户，
并将授权委托给其他账户来设置代理用户。例如，`root` 可以执行下列语句：
```
CREATE USER 'admin'@'localhost' IDENTIFIED BY 'test';
GRANT PROXY ON ''@'' TO 'admin'@'localhost' WITH GRANT OPTION;
```

上面的语句创建了一个名为 `admin` 的用户，他可以管理所有的 `GRANT PROXY` 映射，例
如，`admin` 可以：
```
GRANT PROXY ON sally TO joe;
```

### Default Proxy Users

要指定某些或所有用户都使用某个给定的认证插件进行连接，可以创建一个“空白”的
MySQL 帐户（`''@''`），然后将此账户与要指定的插件关联起来，并让插件返回经过真实
身份验证的用户名（如果与空白用户不同）。

例如，假设存在一个名为 `ldap_auth` 的插件，它实现了 `LDAP` 身份验证，并可以把连
接的用户映射到开发者或管理帐户上。要在这些帐户上设置用户代理，请使用以下语句：
```
-- create default proxy account
CREATE USER ''@'' IDENTIFIED WITH ldap_auth AS 'O=Oracle, OU=MySQL';

-- create proxied accounts
CREATE USER 'developer'@'localhost' IDENTIFIED BY 'developer_pass';
CREATE USER 'manager'@'localhost' IDENTIFIED BY 'manager_pass';

-- grant PROXY privilege to default proxy account for proxied accounts
GRANT PROXY ON 'manager'@'localhost' TO ''@'';
GRANT PROXY ON 'developer'@'localhost' TO ''@'';
```

现在假设客户端连接如下：
```
shell> mysql --user=myuser --password ...
Enter password: myuser_pass
```

服务器找不到 MySQL 用户 `myuser`。但是因为有一个空白的用户帐户（`''@''`）与客户
端用户名和主机名相匹配，服务器就使用此空白账户对客户端进行身份验证：服务器调用空
白账户的 `ldap_auth` 认证插件，并将客户端的 `myuser`和 `myuser_pass` 作为用户名
和密码传递给它。

如果 `ldap_auth` 插件在 LDAP 目录中发现 `myuser_pass` 不是 `myuser` 的正确密码，
那么身份验证就会失败，而服务器拒绝连接。

如果密码是正确的，并且 `ldap_auth` 发现 `myuser` 是一个开发人员，它会将用户名
`developer` 返回给 MySQL 服务器，而不是 `myuser`。

插件返回一个不同于客户端用户名`myuser` 的用户名，这就是给服务器的一个信号，表示
服务器应该将 `myuser` 作为代理用户对待。

服务器接着核实 `''@''` 可以身份验证为 `developer`（因为此空白帐户有 `PROXY` 特权
这样做）并接受连接。这个会话将继续使用 `myuser`，它拥有 `developer`（即被代理用
户） 的特权。（这些特权应该由 DBA 使用 `GRANT` 语句设置，而不是显示）。

`USER()` 和 `CURRENT_USER()` 函数返回这些值：
```
mysql> SELECT USER(), CURRENT_USER();
+------------------+---------------------+
| USER()           | CURRENT_USER()      |
+------------------+---------------------+
| myuser@localhost | developer@localhost |
+------------------+---------------------+
```

如果插件发现 `myuser` 在 LDAP 目录中是经理，它会返回 `manager` 作为用户名，而会
话将继续使用 `myuser` ，且拥有 `manager` 的特权。
```
mysql> SELECT USER(), CURRENT_USER();
+------------------+-------------------+
| USER()           | CURRENT_USER()    |
+------------------+-------------------+
| myuser@localhost | manager@localhost |
+------------------+-------------------+
```

为简单起见，外部认证不能是多级的：在前面的例子中，没有考虑到 `developer` 的凭证
和 `manager` 的凭证。但是，如果客户端试图直接使用 `developer` 或 `manager` 帐户
连接和认证，那么它们仍然可以被使用，这就是为什么这些账户应该被分配密码。

### Default Proxy User and Anonymous User Conflicts

如果您打算创建一个默认的代理用户，请检查其他现有的“匹配任何用户”的账户，它们可
以阻止该用户按预期工作，因为这些帐户优先于默认的代理用户。

在前面的讨论中，默认的代理用户帐户主机部分为空 `''`，匹配任何主机。如果你设置一
个默认的代理用户,也小心检查非代理账户是否存在相同的用户名部分和主机部分为 `'%'`
，因为`'%'`也匹配任何主机，但在服务器内部用来对账户行进行排序的规则里，`'%'` 优
先于 `''`。
参考： Section 6.2.4, “Access Control, Stage 1: Connection Verification”

假设一个 MySQL 安装包括这两个帐户：
```
-- create default proxy account
CREATE USER ''@''
IDENTIFIED WITH some_plugin AS 'some_auth_string';

-- create anonymous account
CREATE USER ''@'%'
IDENTIFIED BY 'some_password';
```

第一个帐户（`''@''`）是打算作为默认的代理用户，用于验证用户的连接，这些用户不能
匹配更具体的帐户。第二个帐户（`''@'%'`）是一个匿名用户帐户，它被创建后可能用于允
许没有自己帐户的用户匿名连接。

这两个帐户都有相同的用户部分（`''`），匹配任何用户。每个帐户都有一个与任何主机相
匹配的主机部分。尽管如此，在连接尝试时账户匹配有一个优先级，因为匹配的规则是主机
`'%'` 排在 `''` 前面。对于那些与任何更具体的帐户不匹配的帐户，服务器尝试对它们进
行身份验证时，是用 `''@'%'`（匿名用户）而不是 `''@''`（默认的代理用户）。结果就
是，默认的代理帐户从未被使用。

为了避免这个问题，请使用以下策略之一：

* 删除匿名帐户，这样它就不会与默认的代理用户发生冲突。如果您想将每个连接与具名用
  户关联起来，这可能是一个好主意。

* 使用一个更具体的默认代理用户，该用户在匿名用户之前匹配。例如，只允许从
  `localhost` 代理连接，使用 `''@'localhost'`：

    ```
    CREATE USER ''@'localhost'
    IDENTIFIED WITH some_plugin AS 'some_auth_string';
    ```
  然后，修改任何 `GRANT PROXY` 语句，使用 `''@'localhost'` 而不是 `''@''` 来命名
  代理用户。

  请注意，这种策略也阻止了来自 `localhost` 的匿名用户连接。

* 建立多个代理用户，一个用于本地连接，另一个用于“其他一切”（远程连接）。这可能
  非常有用，特别是当本地用户应该从远程用户那里获得不同的特权时。
 
  创建代理用户：
    ```
    -- create proxy user for local connections
    CREATE USER ''@'localhost'
    IDENTIFIED WITH some_plugin AS 'some_auth_string';

    -- create proxy user for remote connections
    CREATE USER ''@'%'
    IDENTIFIED WITH some_plugin AS 'some_auth_string';
    ```

  创建被代理的用户：
    ```
    -- create proxied user for local connections
    CREATE USER 'developer'@'localhost'
    IDENTIFIED BY 'some_password';

    -- create proxied user for remote connections
    CREATE USER 'developer'@'%'
    IDENTIFIED BY 'some_password';
    ```

  为代理用户授予对应的被代理用户的代理特权：
    ```
    GRANT PROXY ON 'developer'@'localhost' TO ''@'localhost';
    GRANT PROXY ON 'developer'@'%' TO ''@'%';
    ```

  最后，授予对本地和远程代理用户的适当权限（未举例）。

  假设 `some_plugin/'some_auth_string'` 组合导致 `some_plugin` 将客户端用户名映
  射到 `developer`。则本地连接与代理用户 `''@'localhost'` 相匹配，后者映射到被代
  理用户 `developer@'localhost`。远程连接与代理用户 `''@'%'` 相匹配，后者映射到
  被代理用户 `'developer'@''`。

### Server Support for Proxy User Mapping

一些身份验证插件为自己实现了代理用户映射。对于某些其他的插件，MySQL 服务器本身可
以根据授予的代理特权映射代理用户。如果启用了 `check_proxy_users` 系统变量，服务
器就会为请求代理用户映射的任何身份验证插件执行映射：

* 在默认情况下，`check_proxy_users` 是禁用的，因此即使是身份验证插件要求服务器执
  行代理用户映射，服务器也不会执行。

* 启用 `check_proxy_users` 后，要使用服务器代理用户映射可能还需要启用系统变量
  `plugin-specific`
    * 对于 `mysql_native_password` 插件, 启用 `mysql_native_password_proxy_users`.
    * 对于 `sha256_password` 插件, 启用 `sha256_password_proxy_users`.

由服务器执行的代理用户映射受到以下限制：

* 服务器将不会代理或被代理匿名用户(The server will not proxy to or from an
  anonymous user)，即使匿名用户授予了关联的“PROXY”特权。
* 当单个帐户被授予超过一个被代理帐户的代理特权时，服务器代理用户映射是不确定的。因
  此，不鼓励把多个被代理帐户的代理特权授给单个帐户。

### Proxy User System Variables

两个系统变量有助于跟踪代理登录过程：

* `proxy_user`: 如果没有使用代理，这个值是“NULL”。否则，它将指示代理用户帐户。
  例如，如果客户端通过 `''@''` 代理帐户进行身份验证，该变量被设置如下：
```
mysql> SELECT @@proxy_user;
+--------------+
| @@proxy_user |
+--------------+
| ''@''        |
+--------------+
```

* `external_user`: 有时身份验证插件可以使用外部用户向 MySQL 服务器进行身份验证。
  例如，当使用 Windows 原生(本地，native)身份验证时，使用 Windows API 进行身份验
  证的插件不需要传递给它的登录 ID。但是，它仍然使用 Windows 用户 ID 进行身份验证
  。这个插件可以使用只读会话变量 `external_user` 将这个外部用户 ID（或第一个512
  UTF-8字节）返回给服务器。如果插件没有设置这个变量，那么它的值就是 NULL。

## 6.3.11 User Account Locking

从 5.7.6 版本起，MySQL 支持通过 `CREATER USER` 、`ALTER USER` 的 `ACCOUNT LOCK`
和 `ACCOUNT UNLOCK` 子句来锁定和解锁账户：

* 当与 `CREATE USER` 一起使用时，这些子句指定了新帐户的初始锁定状态。在没有这两
  个子句的情况下，帐户是在一个无锁定状态下创建的。

* 当与 `ALTER USER` 一起使用时，这些子句指定了现有帐户新的锁定状态。在没有这两
  个子句的情况下，帐户保持锁定状态不变。

账户的锁定状态记录在 `mysql.user` 表的 `account_locked` 列里。`SHOW CREATE USER`
的输出表明了账户的锁定状态。

如果一个客户端尝试连接到一个已锁定的账户，将失败。服务器通过增加状态变量
`Locked_connects` 的值来表明尝试连接到一个锁定账户的尝试次数，返回一个
`ER_ACCOUNT_HAS_BEEN_LOCKED` 错误，并在错误日志里写入信息：

```
Access denied for user 'user_name'@'host_name'.
Account is locked.
```

锁定一个帐户并不影响使用一个代理用户来连接的能力，代理用户假定被锁定的帐户的身份
。它也不会影响执行储存的程序或视图的能力，这些程序或视图有一个 `DEFINER` 子句命
名被锁定的帐户。也就是说，使用代理帐户或存储程序或视图的能力不受锁定帐户的影响。

账户锁定能力依赖于 `mysql.user` 表里 `account_locked` 列的存在：
* 从旧版本升级到MySQL 5.7.6 或更新版本后，要运行 `mysql_upgrade` 确保此列存在。
* 对于未升级的安装，没有 `account_locked` 列，服务器则视所有账户没有锁定，如果使
  用 `ACCOUNT LOCK` 和 `ACCOUNT UNLOCK` 子句则产生一个错误。

## 6.3.12 SQL-Based MySQL Account Activity Auditing

应用程序可以使用下面的指导方针来执行基于 SQL 的审计，将数据库活动与 MySQL 帐户联
系起来。

MySQL 账户对应于 `mysql.user` 表里的行。当客户端成功连接时，服务器将客户端验证到
该表中的某一行。该行的 `User` 和 `Host` 列值唯一地标识了此账户，并对应于
`'username'@hostname'` 格式，其中账户名被编写到 SQL 语句里。

用于认证客户端的账户决定了客户端拥有哪些特权。通常，`CURRENT_USER()` 函数可以被
调用以确定对于客户端用户来说是哪个帐户。它的值是由 `mysql.user` 表中对应账户行的
`User` 和 `Host` 列构成的。

然而，在某些情况下，`CURRENT_USER()` 的值不是对应于客户端用户，而是对应于另一个
不同的帐户。当特权检查不是基于客户端帐户时，就会出现这种情况：

* 用 `SQL SECURITY DEFINER` 特性定义的存储例程（程序和功能）
* 用 `SQL SECURITY DEFINER` 特性定义的视图
* 触发器和事件

在这种情况下，特权检查是针对 `DEFINER` 账户的，`CURRENT_USER()` 引用的就是此账户
，而不是引用的调用存储例程或视图或激活了触发器的客户端账户。

为了确定调用用户，您可以调用 `USER()` 函数，该函数返回一个值，表明了客户端提供的
实际用户名和客户端发起连接的主机名。然而，这个值并不一定与 `user` 表中的帐户直接
对应，因为 `USER()` 值从不包含通配符，而帐户值（由 `CURRENT_USER()` 返回）可能包
含用户名和主机名通配符。

例如，一个空用户名匹配任何用户，所以账户 `''@'localhost'` 使得客户端可以从本地主
机以任何用户名连接为一个匿名用户，这种情况下，如果客户端使用用户名 `user1` 从本
地主机连接，则 `USER()` 和 `CURRENT_USER()` 返回不同值：

```
mysql> SELECT USER(), CURRENT_USER();
+-----------------+----------------+
| USER() | CURRENT_USER() | 
+-----------------+----------------+
| user1@localhost | @localhost |
+-----------------+----------------+
```

账户的主机名部分也可以包含通配符，如果主机名包含了 `%` 或 `_` 模式字符或子网掩码
标记，则此账户可被客户端从多个主机连接，但 `CURRENT_USER()` 不会表明具体是哪台主
机。例如，账户 `'user2'@'%.example.com'` 可被 `user2` 从域 `exmaple.com` 的任何
主机进行连接，如果 `user2` 从 `remote.example.com` 连接，则两个函数的返回值：

```
mysql> SELECT USER(), CURRENT_USER();
+--------------------------+---------------------+
| USER() | CURRENT_USER() |
+--------------------------+---------------------+
| user2@remote.example.com | user2@%.example.com |
+--------------------------+---------------------+
```

如果程序必须调用 `USER()` 用于用户审计（例如触发器内部执行审计），且必须关联
`USER()` 值和 `user` 表里的账户，则避免账户的 `User` 和 `Host` 列包含通配符是必
要的。特别是不允许 `User` 列为空，不允许主机值里包含模式字符和子网掩码。即所有的
账户都必须有一个非空的 `User` 值和字面意义的 `Host` 值。

关于前面的例子，`''@'localhost'` 和 `'user2'@'%.example.com'` 账户应该改为不使用
通配符：

```
RENAME USER ''@'localhost' TO 'user1'@'localhost';
RENAME USER 'user2'@'%.example.com' TO 'user2'@'remote.example.com';
```

如果 `user2` 必须从 `exmaple.com` 域里的几个不同的主机连接，则应为每个主机分配一
个单独的账户：

要从 `USER()` 或 `CURRENT_USER()` 提取用户名或主机名，使用 `SUBSTRING_INDEX` 函数：
```
mysql> SELECT SUBSTRING_INDEX(CURRENT_USER(),'@',1);
+---------------------------------------+
| SUBSTRING_INDEX(CURRENT_USER(),'@',1) |
+---------------------------------------+
| user1 |
+---------------------------------------+

mysql> SELECT SUBSTRING_INDEX(CURRENT_USER(),'@',-1);
+----------------------------------------+
| SUBSTRING_INDEX(CURRENT_USER(),'@',-1) |
+----------------------------------------+
| localhost |
+----------------------------------------+
```
