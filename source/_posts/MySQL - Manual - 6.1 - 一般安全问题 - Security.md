---
title: MySQL - Manual - 6.1 - 一般安全问题 - Security
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

一般安全问题

<!--more-->

## 安全参考

在讨论安全性时，有必要考虑完全保护整个服务器主机（而不仅仅是 MySQL 服务器），以
应对所有类型的适用攻击：窃听、更改、回放和拒绝服务。

MySQL 使用基于访问控制列表（ACLs）的安全性，用于所有连接、查询和用户可以尝试执行
的其他操作。还支持 MySQL 客户端和服务器之间的 ssl 加密连接。

这里讨论的许多概念都不是针对MySQL的;几乎所有的应用程序都适用相同的通用思想。

运行 MySQL 时，遵循这些指导方针：

1. 最重要的：不要让除了 MySQL 的 root 账户之外的任何用户访问 mysql 数据库里的
   user 表

2. 了解 MySQL 访问权限系统是如何工作的。使用 GRANT 和 REVOKE 语句来控制对 MySQL
   的访问。只授予必要的权限。从来不要向所有主机授权。

   检查清单；

   2.1. 尝试 `mysql -u root`，如果登录时没有被要求密码且成功了则表示任何人都可以
   用 MySQL 的 root 账号全权限登录此 MySQL 服务器。修正问题请重新阅读 MySQL 安装
   指导关于为 root 设置密码的部分 Section 2.10.4, “Securing the Initial MySQL
   Accounts”

   2.2. 使用 `SHOW GRANTS` 语句查看哪个账户拥有访问什么的权限，然后使用 `REVOKE`
   语句移除不必的权限

3. 不要在数据库中保存明文密码。应使用 SHA2() 等单向散列函数并存储其散列值。但为
   了防止使用彩虹表一类的技术来破解密码，不要把散列函数直接应用于密码上，而是额
   外的再使用一些儿字符串来作为盐值，使用下列方法： hash(hash(password)+salt)
   ??? 彩虹表

4. 不要从字典中选择密码，有专门的程序来破解密码。

   像 `xfish98` 这样的密码也很糟，更好的是 `duag98`，即打字时把手向左移动一位再
   打`fish` 即得到 `duag`。

   另一个方法是使用一句话的头一个字母组成密码，如 `Four score and seven years
   ago` 得到密码 `Fsasya`，还可以用数字 4 代替 `Four` 数字 7 代替 `seven` 得到更
   复杂的密码 `4sa7ya`，这样的密码很容易记住和拼写但对于不知道这个句子的人来说却
   很难猜中。

5. 使用防火墙，至少可以帮你抵挡 50% 以上各种类型的攻击。把 MySQL 服务器放到防火
   墙后面或者隔离区(DMZ)。

   检测清单：

   5.1. 尝试从互联网上使用 nmap 一类的工具扫描你的服务器端口，MySQL 默认使用
   3306 端口，这个端口应该不能被不信任的主机访问到。检测你的 MySQL 端口是否打开
   的简单方法：`telnet server_host 3306`，如果 telnet 挂起或被拒绝，则说明端口如
   期望的被屏蔽了，如果连接成功且返回了一些儿垃圾字符，则说明端口是很危险的开放
   着，除非有很好的理由否则应该关闭。

6. 访问 MySQL 的应用程序不应该信任用户输入的任何数据，并且应该使用适当的防御性编
   程技术，参见： Section 6.1.7, “Client Programming Security Guidelines”.

7. 不要在互联网上传输未经加密的数据，有能力的用户可能拦截数据并按自己的意愿来使
   用它们。应该使用加密的协议，例如：SSH 或 SSL。MySQL 支持内部 SSL 连接，另一个
   方法是使用 SSH 端口转发来为通信建立加密隧道。

8. 学习使用 tcpdump 和 strings 应用。大多数时候，可以通过使用类似下面的命令来检
   测 MySQL 数据流是否为非加密的：
   ```
   shell> tcpdump -l -i eth0 -w - src or dst port 3306 | strings
   ```
   注意，即使使用上面的命令没有看到明文数据，也不是意味着数据总是经过加密的

## 保证密码安全

### 密码安全之终端用户

#### 客户端程序的密码

使用客户端程序连接到 MySQL 服务器时，可以通过多种形式来指定密码，其风险也不相同

1. 在命令行上使用 `-p` 或 `--password` 选项，但不给出值，由客户端程序交互的来请
   求密码值，密码输入时也会以 `*` 代替。
   ```
   shell> mysql -u francis -p db_name
   Enter password: ********
   ```
   这种方式比在命令行上直接明文写出密码安全得多，但只适用于交互式的程序，如果通
   过脚本调用客户端程序，则无法使用上述方法。
   某些系统上脚本的第一行会被读取并错误的解释为密码。???

2. 把密码保存在选项文件里。Unix 系统里，可以把密码写在家目录下的选项文件
   `.my.conf` 的 `[client]` 章节：
   ```
   [client]
   password=your_pass
   ```
   为了保证此选项文件的安全，设置此文件只能被你自己访问:
   ```
   shell> chmod 600 .my.cnf
   OR
   shell> chmod 400 .my.cnf
   ```
   若想指定其他包含密码的选项文件，使用选项 `--defaults-file=file_name`
   ```
   shell> mysql --defaults-file=/home/francis/mysql-opts
   ```
   参见：Section 4.2.6, “Using Option Files”

3. 把密码保存在系统环境变量 `MYSQL_PWD` 中，这种保存密码的方法是非常不安全的。

   `ps` 程序的一些儿版本包含有查看正运行进程的环境变量的选项，在某些系统上，其他
   用户运行此程序则密码就被暴露。

   即使一些系统上没有 `ps` 程序，也不能这样保存，因为假定没有其他方法来检测进程
   的环境变量是不明智的。

   保存到系统变量参见：Section 4.9, “MySQL Program Environment Variables”.

#### 历史文件中的密码

##### mysql 历史文件

在 Unix 上，mysql 客户端会将执行语句的记录写到历史文件中(参见:Section 4.5.1.3,
“mysql Logging”)。

这个历史文件默认是家目录下的 `.mysql_history`，在一些儿 SQL 语句如 `CREATE
USER`, `ALTER USER` 中，密码以明文的形式写下来，也会被以明文记录到历史中，所以也
要保证此文件的安全，和 `.my.conf` 的操作一样
```
shell> chmod 600 .mysql_history
OR
shell> chmod 400 .mysql_history
```

##### shell 历史文件

如果命令行解释器 shell 程序也被配置为维护着一个命令历史文件，例如 bash 使用的
`~/.bash_history`，则此文件中也可能保存着 MySQL 的密码，因此任何此类的文件都应该
设置为限制访问模式
```
shell> chmod 600 history_file
OR
shell> chmod 400 history_file
```

### 密码安全之管理员

MySQL 把用户密码保存在 `mysql.user` 表里，永远不要把此表的访问权限授予非管理账户
参见：Section 6.3.7, “Password Management”, 

用户密码会过期，所以用户必须重新设置密码。
参见：Section 6.3.8, “Password Expiration and Sandbox Mode”.

插件 `validate_password` 可以用于检测密码是否符合密码策略
Section 6.5.3, “The Password Validation Plugin”.

如果一个用户可以通过修改系统变量 plugin_dir 的值来更改插件目录，或者是通过
`.my.cnf` 文件指定插件目录位置，那他就可以替换插件并修改插件提供的功能，包括身份
验证插件。

像日志文件一类的可能记录密码的文件需要设置受限权限。

### 密码和日志

在 `CREATE USER`, `GRANT`, `SET PASSWORD` 等 SQL 语句中，密码可能被以明文的形式
出现，如果执行语句被 MySQL 记录到查询日志，则有权限读取日志的人会看到密码。

为了避免密码以明文写入普通查询日志，慢查询日志和二进制日志中，下述语句中的明文密
码会被重写:
```sql
CREATE USER ... IDENTIFIED BY ...
ALTER USER ... IDENTIFIED BY ...
GRANT ... IDENTIFIED BY ...
SET PASSWORD ...
SLAVE START ... PASSWORD = ...
CREATE SERVER ... OPTIONS(... PASSWORD ...)
ALTER SERVER ... OPTIONS(... PASSWORD ...)
```
注意密码重写只适用于上述语句，不适用于其他语句，例如对于 `mysql.user` 表的
`INSERT` `UPDATE` 可能涉及到明文密码被日志记录，所以应该避免这样的语句，另外对授
权表直接操作也是不鼓励的。

对于普通查询日志，可以在启动 MySQL 服务器时通过 `--log-raw` 选项来取消密码重写，
为了安全，不建议再生产环境使用此选项；此选项可用于调试时，可以清除的查看服务器接
受到的原始文本。

审计日志插件生成的审计日志文件内容没有加密，为了安全性，这个文件应该被保存在只允
许 MySQL 服务器访问的目录，或者是有正当理由查看日志的用户可以访问的目录。
参考：Section 6.5.5.3, “MySQL Enterprise Audit Security Considerations”.

如果安装了查询重写插件，则服务器收到的查询语句可能被重写，这种情况下，选项
`--log-raw` 对日志记录的查询语句遵循下列规则：

1. 不使用 `--log-raw`，服务器记录到日志的查询语句来自于查询重写插件，这时的查询
   语句可能和接收到的原始语句不同。

2. 使用了 `--log-raw`，则服务器直接记录收到的原始查询语句到日志。

密码重写的含义是：不能被解析的 SQL 语句不会写入普通查询日志，因为它们不能被认为
是无密码的

需要记录所有语句的用例，包括那些有错误的语句，应该使用 `--log-raw` 选项，记住这
也可以绕过密码重写。

只有 SQL 语句语法需要输入明文密码时才会进行密码重写，如果语句的语法需要的是密码
散列值，则不会密码重写，即使需要散列值的地方错误的使用了明文，也不会重写，会被如
实的记录到日志。(expect plain text passwords OR expect a password hash value)

例如，下面语句将会被按原文记录到日志，因为这里本来期望/需要的是密码散列值，但错
误的传递了明文密码：
```sql
CREATE USER 'user1'@'localhost' IDENTIFIED BY PASSWORD 'not-so-secret';
```

为了避免日志文件不必要的曝光，应把日志文件放到对服务器和数据库管理员都限制访问的
目录里。如果服务器把日志记录到了 mysql 数据库的表里，则可以授权数据库管理员访问
这些表。

Replication slaves store the password for the replication master in the master
info repository, which can be either a file or a table.
参考：Section 16.2.4, “Replication Relay and Status Logs”
复制从服务器把用于复制主服务器的密码保存在了主信息库中，主信息库可以是个文件或者
数据库表，需确保主信息库只能被数据库管理员访问。

把密码保存在文件里的另一种方法是使用 `START SLAVE` 语句来指定连接到主服务器的凭证

使用限制访问模式来保护数据库备份，里面有包含密码的日志表和日志文件。

### MySQL 中的密码散列

#### 适用版本

本节中的信息仅适用于 MySQL 7.5.5 之前，并且仅适用于使用 `mysql_native_password`
或 `mysql_old_password` 身份验证插件的账户。

MySQL 5.7.5 移除了对 `pre-4.1` 密码散列的支持，这包括移除 `mysql_old_password`
身份验证插件和 `OLD_PASSWORD()` 函数。另外，`secure_auth` 不能被禁用，
`old_passwords` 不能被设置为1。

从 MySQL 5.7.5 起只有 `mysql_native_password` 验证插件仍然和 `4.1` 密码散列相关。

注：`4.1` 和 `pre-4.1` 是两套散列

#### MySQL 中使用密码的地方

MySQL 的 mysql 数据库 user 表里列出了用户账号，账户密码是以散列值存储的.

在客户端/服务通讯的过程中，MySQL 有两个阶段会使用到密码：

* 客户端连接到服务器时，需要一个初始化身份验证的步骤，客户端需提供账号和密码，其
  散列值和数据库 user 表中的匹配。

* 客户端连接后，如果有足够的权限则可以设置或更改账户密码：通过 `PASSWORD()` 方法
  产生散列值，或通过下列语句：CREATE USER, GRANT, SET PASSWORD

#### MySQL 中的密码散列变更历史

MySQL 中的密码散列方法有变化过程，这些变化可以通过 `PASSWORD()` 方法的变化和用户
表的结构变化来说明。

##### 最原始的散列方法 Pre-4.1

原始的散列方法产生的是一个 16 字节的字符串：
```sql
mysql> SELECT PASSWORD('mypass');
+--------------------+
| PASSWORD('mypass') |
+--------------------+
| 6f8c114b58f2ce9e |
+--------------------+
```
所以为了存储账号密码，user 表的 Password 列在这时候是 16 字节长

##### 散列方法 4.1

MySQL 4.1 引入了密码散列，它提供了更好的安全性，并降低了密码被拦截的风险。

MySQL 4.1 的变化有几个方面：

* PASSWORD() 方法产生的密码值格式不同
* 扩展了 Password 列
* 默认散列方法的控制
* 对试图连接到服务器的客户机的允许散列方法的控制

MySQL 4.1 的变化分为两个阶段:

* 先在 MySQL 4.1.0 中使用了 MySQL 4.1.1 的散列方法的初级版本，这个版本很短命后面
  不再讨论。
* 然后在 MySQL 4.1.1 中，散列方法被修改为产生更长的 41 字节散列值
```sql
mysql> SELECT PASSWORD('mypass');
+-------------------------------------------+
| PASSWORD('mypass') |
+-------------------------------------------+
| *6C8989366EAF75BB670AD8EA7A7FC1176A95CEF4 |
+-------------------------------------------+
```

较长的密码散列格式具有更好的加密属性，基于更长散列的客户端身份验证比旧的短散列更
安全。

为了适应更长的散列值，user 表的 Password 列也在这时被更改为 41 字节长，这也是此
列目前的长度。

因此目前 user 表的 Password 列既可以存储 `pre-4.1` 格式的 16 字节散列值也可以存
储 `4.1` 格式的 41 字节散列值，对于一个给定的散列值，可以根据下面两种方法来确定
使用的是哪个格式的散列：
1. 长度：`4.1` 长度为 41 字节，`pre-4.1` 长度为 16 字节
2. 内容：`4.1` 散列值总是以 `*` 号开头，而 `pre-4.1` 则不是

为了允许显式的生成 `pre-4.1` 格式的密码散列，还进行了两次更改：

1. 添加了 `OLD_PASSWORD()` 方法，返回 16 字节长散列值

2. 为了兼容性，添加了系统变量 `old_passwords`，以便 DBAs 和应用程序控制散列方法
   `PASSWORD()`，`old_passwords` 默认值为 0，表示使用 `4.1` 的 41 字节长散列方法
   ，若为 1，则表示使用 `pre-4.1` 的散列方法，这时 `PASSWORD()` 和
   `OLD_PASSWORD()` 等价。
   为了允许 DBA 控制客户端如何连接，添加了系统变量 `secure_auth`，启动服务器时根
   据此变量的启用或禁用状态将可以决定是否允许客户端使用老旧的 `pre-4.1` 散列方法
   ，在 MySQL 5.6.5 之前，`secure_auth` 默认是禁用的，即可以用旧的 `pre-4.1`，从
   MySQL 5.6.5 起，`secure_auth` 默认是启用的，但 DBAs 可以禁用掉它，但不推荐这
   样做，`pre-4.1` 应被弃用并避免再次使用它。

另外，客户端 `mysql` 支持选项 `--secure-auth` 类似于服务器端的系统变量
`secure_auth`，但这是客户端的选项，可以用来阻止客户端连接到使用 `pre-4.1` 散列方
法的不安全账户。

账户升级说明，参考：Section 6.5.1.3,“Migrating Away from Pre-4.1 Password
Hashing and the mysql_old_password Plugin”.

#### 与散列方法相关的兼容性问题

以后补充

## 使 MySQL 对攻击者安全

使用客户端连接到服务器时，应该使用密码，密码不是使用明文传输的，密码在 MySQL
4.1.1 版本里已经被升级为很安全的版本，如果使用的是 pre-4.1 散列方法则密码可能被
破解，因为有能力的入侵者可以监听客户端和服务器之间的通讯。

为了使得通讯也不可被监听，可以使用 MySQL 的内部 SSL。
参见： Section 6.4, “Using Encrypted Connections”

或者，使用 SSH 获得一个加密的 TCP/IP 连接。
开源 SSH 客户端：http://www.openssh.org/
SSH 客户端的开源版和商业版比较：http://en.wikipedia.org/wiki/Comparison_of_SSH_clients

为了 MySQL 系统安全，强烈地考虑以下建议：

1. 所有的 MySQL 账号都必须设置密码，如果有账户没有设置密码，则任何人都可以简单的
   通过 mysql 客户端连接到服务器
   ```
   shell>> mysql -h host -u user_no_passwd
   ```
   设置密码的方法的讨论：Section 6.3.6, “Assigning Account Passwords”.

2. 确保运行 mysqld 的帐户是唯一有权限在数据库目录中读或写的 Unix 用户帐户。

3. 永远不要以 Unix 的 root 用户身份运行 mysqld，这是极度危险的，因为任何具有文件
   权限的用户都可以让 MySQL 服务器以 root 的身份创建文件，例如 `~root/.bashrc`，
   因此除非明确的指定了 `--user=root` 选项，mysqld 拒绝以 root 身份运行。

   mysqld 应该以一个普通的没有特权的用户身份运行，可以创建一个名为 mysql 的 Unix
   用户账号，此账户只用来管理 MySQL 服务器。

   以指定的用户账号来启动服务器，可以在选项文件 `my.cnf` 里设置：
   ```
   [mysqld]
   user=mysql
   ```
   这时，MySQL 服务器无论是手动启动，还是由 mysqld_safe 或 mysql.server 启动，均
   是以指定的用户来运行的。

   以除 root 以外的其他 Unix 用户运行 mysqld 并不意味着需要改变用户表中的 root
   用户名。MySQL 帐户的用户名与 Unix 帐户的用户名没有任何关系。

   参见：Section 6.1.5, “How to Run MySQL as a Normal User”.

4. 不要向非管理用户授予文件权限。任何拥有文件权限的用户都可以在文件系统的任何地
   方使用 mysqld 守护进程的特权编写文件。包括服务器的数据目录，其中包含实现特权
   表的文件。想要文件特权操作安全一点儿，使用 `SELECT ... INTO OUTFILE` 生成文件
   时不要覆盖已经存在的文件，而且文件要对所有人都是可写的。

   文件权限也可以被用来读取任何的文件，只要此文件对于以某用户身份运行的服务器具
   有世界可读性或可访问性

   使用此权限，可以把任何文件读入数据库表。

   但此权限也可能被滥用，例如，通过 `LOAD DATA` 把 `/etc/passwd` 读入到表里，则
   会通过`SELECT` 语句泄露.

5. 限制可以写入和读取文件的目录，设置系统变量 `set_secure_priv` 到指定的目录，参
   见：Section 5.1.5, “Server System Variables”

6. 不要向非管理用户授予 `process` 和 `super` 权限。`mysqladmin processlist` 和
   `SHOW PROCESSLIST` 显示了当前执行的语句文本，所以允许查看服务器进程列表的用户
   可能看见被其他用户执行的敏感语句如 `UPDATE user SET
   password=PASSWORD('not_secure')`

   mysqld 为拥有 `SUPER` 权限的用户准备了一个额外的连接，所以 MySQL 的 root 用户
   可以在其他普通连接都在使用的情况下登录并检查服务器的活动情况。`SUPER` 特权可
   以用来终止客户端连接，通过改变系统变量的值来改变服务器操作和控制复制服务器.

7.  不允许把符号连接用于表。可以通过 `--skip-symbolic-links` 选项来禁用，当使用
    root 用户来运行 mysqld 时更是特别重要，因为任何对服务器数据目录有写权限的用户
    都可以删除任何系统文件。

    参见：Section 8.12.3.2, “Using Symbolic Links for MyISAM Tables on Unix”.

8.  存储的程序和视图应该使用第23.6节中讨论的安全指南来编写

    参见：Section 23.6,“Access Control for Stored Programs and Views”.

9.  如果无法信任 DNS 服务器，则应该在授权表里使用 IP 地址而不是主机名，总之，应该
    在使用包含通配符的主机名创建授权表条目时要特别小心。

10. 如果要限制一个账户允许的连接数量，可以通过设置 mysqld 变量
    `max_user_connections` 的值。`GRANT` 语句也支持资源控制选项，来限制帐户的服
    务器使用范围

    参见；Section 13.7.1.4, “GRANT Syntax”.

11. 如果插件目录对服务器是可写入的，那么用户就可能使用 `SELECT...INTO DUMPFILE`
    语句将可执行代码写入插件目录中的文件。可以通过让 `plugin_dir` 对服务器只读来
    阻止，或者通过设置`--secure-file-priv` 到一个安全目录，即此目录里可以安全的
    用 `SELECT` 进行写操作。


## 与安全相关的 mysqld 选项和变量

稍后补充

## 如何以普通用户身份运行 MySQL

在 Windows 上，您可以使用普通用户帐户将 MySQL 服务器作为 Windows 服务运行。

在 Linux 上，对于使用 MySQL 存储库、RPM 包或 Debian 软件包执行的 MySQL 安装，
MySQL 服务器 mysqld 应该使用本地操作系统用户 `mysql` 来启动，包含在安装程序里的
初始化脚本不支持使用其他的本地操作系统用户来启动。

在 Unix 上，或使用 `tar`, `tar.gz` 执行 MySQL 安装的 Linux 上，MySQL 服务器
mysqld 可以被任何用户启动和运行，但是为了安全原因，仍应该避免使用 `root` 用户来
运行。

Linux 上把 MySQL 的运行用户改为普通的无特权用户 `user_name`，需要执行下列操作：

1. 如果服务器在运行，则使用 `mysqladmin shutdown` 停止运行服务器

2. 更改数据库的目录、文件以使得普通用户有权限写入和读取文件：

    ```
    shell> chown -R user_name /path/to/mysql/datadir
    ```
    * 可能需要以 `root` 身份执行
    * 不做这步则服务器以普通用户身份运行时可能无法读取数据库和表
    * 如果 MySQL 数据目录中的目录或文件是符号链接，`chown -R` 可能不会跟踪更改到
        原文件，这样的话则需要用户自己跟踪更改原文件的所有者

3. 启动服务器时以 `user_name` 用户启动，另一个方法是启动时使用 `root` 用户启动，
   但使用 `--user=user_name` 选项，则 mysqld 启动后，在接受任何连接前将切换为以
   `user_name` 身份运行。所以授权表里的 `root` 账号必须总是设置密码，否则任何账
   户都可以使用 `--user=root` 选项执行任何操作。
   参见：Section 2.10.4,“Securing the Initial MySQL Accounts”

4. 为了设定 MySQL 服务器在系统启动时自动以 `user_name` 身份运行，可以通过 `/etc`
   目录或服务器数据目录下的 `my.cnf` 选项文件设置：

    ```
    [mysqld]
    user=user_name
    ```

## 使用 LOAD DATA LOCAL 的安全问题

`LOAD DATA` 语句可以载入服务器上的文件，`LOAD DATA LOCAL` 则可以载入客户端的文件
，载入客户端文件时有两个安全问题：

1. 从客户端主机到服务器主机的文件的传输是由 MySQL 服务器发起的。理论上打过补丁的服务器
   可以告诉客户端程序传送服务器选择的文件，而不是在 `LOAD DATA` 语句中由客户端命
   名的文件，这样的服务器可以访问客户端主机（客户端用户拥有读权限的那台主机）上
   的任何文件，打过补丁的服务器实际上应该回应任何语句要求的文件传输请求，而不仅
   仅是 `LOAD DATA LOCAL`，更基本的问题是客户端不应该连接到不信任的服务器。

2. 在 Web 环境里，客户端连接到 Web 服务器，则 Web 客户端用户可以读取 Web 服务器
   进程有读权限的所有文件，在这个环境里，MySQL 服务器的客户端其实是 Web 服务器，
   而不是连接到 Web 服务器的用户运行的某个远程程序。

为了避免 `LOAD DATA` 问题，客户端应该避免使用 `LOCAL`；

为了避免连接到不信任的服务器，客户端应该建立安全连接并且通过连接时使用
`--ssl-mode=VERIFY_IDENTITY` 选项和适当的 CA 证书来验证服务器的身份

使管理员和应用程序能够管理本地数据加载能力，`LOCAL` 配置是这样工作的：

1. 服务器端

    * 系统变量 `local_infile` 控制服务器端的 `LOCAL` 能力，取决于 `local_infile`
      的设置，服务端将拒绝或允许已启用 `LOCAL` 的客户端进行本地数据加载。缺省是
      启用的。

    * 为了显式的让服务端拒绝或允许 `LOAD DATA LOCAL` 语句（无论客户端的程序和库
      在编译和运行时如何配置），则需要启动 mysqld 时设定 `local_infile`，或者在
      运行时设定。

2. 客户端

    * CMake 的 `ENABLED_LOCAL_INFILE CMake` 选项, 控制着 MySQL 客户端库内编译的
      的默认 `LOCAL` 功能，所以，没有明确安排的客户端可以根据在 MySQL 构建时设定
      的 `ENABLED_LOCAL_INFILE` 设置禁用或启用 `LOCAL` 功能。

      默认情况下，MySQL 二进制发行版里的客户端库编译时启用了
      `ENABLED_LOCAL_INFILE`，如果你自己从源代码编译 MySQL 时，禁用还是启用
      `ENABLED_LOCAL_INFILE` 基于没有明确安排的客户端是否应该具有 `LOCAL` 的功能

    * 使用 C 语言 API 的客户端程序可以通过调用 `mysql_options()` 来来显式地控制
      负载数据加载，从而启用或禁用 `MYSQL_OPT_LOCAL_INFILE` 选项

      参考：Section 27.8.7.50, “mysql_options()”

    * 对于 mysql 客户端，默认本地数据装载是禁用的，明确的禁用或启用此功能，可以
      使用 `--local-infile=0` 或 `--local-infile[=1]`。

    * 对于 mysqlimport 客户端，默认本地数据装载是禁用的，明确的禁用或启用此功能
      ，可以使用 `--local=0` 或 `--local[=1]`。

    * 如果在 Perl 脚本或其他程序里通过读取选项文件里的 `[client]` 组使用 `LOAD
      DATA LOCAL`，可以在组里添加 `local-infile` 选项，为了防止有程序不明白此选
      项，可以在前面添加前缀 `loose-`

      ```mysql
      [client]
      loose-local-infile=0
      ```
    * 在所有情况中，成功使用 `LOCAL` 加载操作都需要客户端的允许。

如果 `LOCAL` 功能被禁用，则无论是因为客户端还是服务器端，客户端在执行 `LOAD DATA
LOCAL` 语句时会收到如下的错误消息：
```
ERROR 1148: The used command is not allowed with this MySQL version
```

## 客户端编程安全指导方针

访问 MySQL 的应用程序不应该信任用户输入的任何数据，它们可能通过在 Web 表单、URL
等输入特殊字符或转义字符来欺骗你的代码，所以要确保自己的程序在遇到这些情况时仍能
保证安全。如一个极端例子：`DROP DATABASE mysql;` 会引起数据丢失

一个常犯的错误是只保护字符串值，数值值也应该要检测。例如：`SELECT * FROM table
WHERE ID=234` 如果数值 234 换为了 `234 OR 1=1` 则将暴露所有数据行并加重服务器负
载，防止此类攻击的简单方法是把数值常量用单引号引起来，即 `SELECT * FROM
table WHERE ID='234'`，即使用户输入了多余字符也会变为字符串一部分，因为在一个数
值的上下文中，MySQL 会自动把尾部的非数字的字符剥离并把字符串转换为数字。

有时人们认为，如果数据库只包含公开可用的数据，就不需要保护，这是不正确的，即使数
据库里的任何行都允许显示，仍需要防范拒绝服务攻击（例如前面例子中那些很浪费服务器
资源的攻击），否则服务器会对合法用户的反馈很迟钝。

检查清单：

1. 启用严格的 SQL 模式以让服务器对于接受的数据值更严格
   参考：Section 5.1.8, “Server SQL Modes”

2. 在所有的 Web 表单中输入单引号和双引号以触发错误，有任何错误则立即改正。

3. 尝试通过添加 `%22(")`、`%23(#)` 和 `%27(')` 来修改动态 url。

4. 尝试修改动态 URLs 里的数据类型，使用上条的方法从数值修改为字符类型，应用应该
   对类似的攻击是安全的

5. 试着在数字字段中输入字符、空格和特殊符号，你的应用程序应该在将它们传递给
   MySQL 之前删除它们，否则就会产生错误。没有经过检测的值传给 MySQL 是非常危险的

6. 把数据传递给 MySQL 之前检测数据的长度

7. 应用程序连接到数据库时使用的用户名应该和用于管理数据库的用户名不同，不要给应
   用程序任何不必需的访问权限。

许多 API 提供了转义数据值中的特殊字符的方法，如果使用得当，将阻止用户输入意外的
值从而导致应用程序生成与您意图不同的语句

1. MySQL C API: 使用 `mysql_real_escape_string_quote()` API 调用.

2. MySQL++: 为查询流使用 `escape` 和 `quote` 修饰符

3. PHP: 使用 `mysqli` 或 `pdo_mysql` 扩展，不要使用老旧的 `ext/mysql` 扩展。

   首选的 API 应支持改进的 MySQL 认证协议和密码，以及使用占位符的预处理语句。参考：
   https://dev.mysql.com/doc/apis-php/en/apis-php-mysqlinfo.api.choosing.html，

   如果必须使用老旧的 `ext/mysql` 扩展，则请使用方法
   `mysql_real_escape_string_quote()` 不要使用 `mysql_escape_string()` 或
   `addslashes()`，因为只有第一个方法是 character set-aware，其他方法在使用无效的
   多字节字符集时都可以被绕过 

4. Perl DBI：使用占位符或 `quote()` 方法

5. Ruby DBI：使用占位符或 `quote()` 方法

6. Java JDBC：使用 `PreparedStatement` 对象和占位符

其他开发语言也应该有类似的功能。
