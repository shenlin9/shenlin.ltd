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

MySQL 5.7.5 中只有 `mysql_native_password` 验证插件仍然和 `4.1` 密码散列相关。

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
储 `4.1` 格式的 41 字节散列值

#### 与散列方法相关的兼容性问题



## 使 MySQL 对攻击者安全



## 与安全相关的 mysqld 选项和变量



## 如何将 MySQL 作为普通用户运行



## 使用 LOAD DATA LOCAL 的安全问题



## 客户端编程安全指导方针


