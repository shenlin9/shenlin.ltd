---
title: Interest - 英语句型理解 - English
categories:
  - Interest
tags:
  - English
---

英语句型理解

<!--more-->

`--syslog` causes error messages to be sent to syslog on systems that support
the logger program


When you use mysqld_safe to start mysqld, mysqld_safe arranges for error (and
notice) messages from itself and from mysqld to go to the same destination.


The mysqld_safe script is written so that it normally can start a server that
was installed from either a source or a binary distribution of MySQL


mysql.server is the script name as used within the MySQL source tree.


The general_log system variable controls logging to the general query log for
the selected log destinations.


If you flush the logs using FLUSH ERROR LOGS, FLUSH LOGS, or mysqladmin
flush-logs, the server closes and reopens any error log file to which it is
writing.
IF 。。。。后面没有使用 will closes and reopens 等将来时态


The ID included in error log messages is that of the thread within mysqld
responsible for writing the message.


If the value is greater than 2, the server logs aborted connections and
access-denied errors for new connection attempts.
logs 是动词，而不是 aborted，for 是为了新的链接尝试去记录错误，而不是记录新连接
的错误


Access control and security within the database system itself, including the
users and databases granted with access to the databases, views and stored
programs in use within the database.


The security is related to the grants for individual users, but you may also
wish to restrict MySQL so that it is available only locally on the MySQL server
host, or to a limited set of other hosts.

Ensure that you have adequate and appropriate backups of your database files,
configuration and log files. Also be sure that you have a recovery solution in
place and test that you are able to successfully recover the information from
your backups. 
确保您对数据库文件、配置和日志文件有足够和适当的备份。还要确保你有一个恢复的解决
方案并且测试你能够成功地从你的备份中恢复信息
这里的测试是否翻译为测试过？英语表示干过某事不是应该过去式吗？


huawei launches laptop with camera that statys covered until it's opened
华为发布了一款笔记本电脑,这个笔记本电脑摄像头平时是隐藏的,在被用到时才会升起


Account passwords can be expired so that users must reset them
会过期？？？要过期？
可能过期?
可以过期？


A user who has access to modify the plugin directory (the value of the
plugin_dir system variable) or the my.cnf file that specifies the plugin
directory location can replace plugins and modify the capabilities provided by
plugins, including authentication plugins.
一个用户如果可以通过修改系统变量 plugin_dir 的值来更改插件目录，或者是通过
my.cnf 文件指定插件目录位置，则他就可以替换插件并修改插件提供的功能，包括身份验
证插件。



An implication of password rewriting is that statements that cannot be parsed
(due, for example, to syntax errors) are not written to the general query log
because they cannot be known to be password free.
are not written to 不会写入
due 含有
password free 免密码
密码重写的含义是：不能被解析的 SQL 语句不会写入普通查询日志，因为它们不能被认为
是无密码的




Password hashing methods in MySQL have the history described following.
These changes are illustrated by changes in the result from the PASSWORD()
function that computes password hash values and in the structure of the user
table where passwords are stored.


The closest some of us will ever get to heaven


Any user that has this privilege can write a file anywhere in the file system
with the privileges of the mysqld daemon.
任何拥有这种特权的用户都可以在文件系统的任何地方使用mysqld守护进程的特权编写文件
？能不能改为下面的顺序？
Any user that has this privilege can write a file anywhere with the privileges
of the mysqld daemon in the file system


To make FILE-privilege operations a bit safer, files generated with SELECT ...
INTO OUTFILE do not overwrite existing files and are writable by everyone.
想要文件特权操作安全一点儿，使用 `SELECT ... INTO OUTFILE` 生成文件时不要覆盖已
经存在的文件，而且文件要对所有人都是可写的。


The FILE privilege may also be used to read any file that is world-readable or
accessible to the Unix user that the server runs as.
world-readable ???



If the plugin directory is writable by the server, it may be possible for a user
to write executable code to a file in the directory using SELECT ... INTO
DUMPFILE.
This can be prevented by making `plugin_dir` read only to the server or by
setting `--secure-file-priv` to a directory where `SELECT` writes can be made
safely.

is writable by  由服务器写入？？
is writable for 


Such a server could access any file on the client host to which the client user
has read access


The ENABLED_LOCAL_INFILE CMake option controls the compiled-in default LOCAL
capability for the MySQL client library.
compiled-in ???


Clients that make no explicit arrangements therefore have LOCAL capability
disabled or enabled according to the ENABLED_LOCAL_INFILE setting specified at
MySQL build time.
所以，没有明确安排的客户端可以根据在 MySQL 构建时设定的 `ENABLED_LOCAL_INFILE`
设置禁用或启用 `LOCAL` 功能
therfore 的位置为什要放那里???




The preferred API's support the improved MySQL authentication protocol and
passwords, as well as prepared statements with placeholders
谓语是 support? 主语？？？



because only mysql_real_escape_string_quote() is character set-aware; the
other functions can be “bypassed” when using (invalid) multibyte character sets
character set-aware????


You cannot specify that a user has privileges to create or drop tables in a
database but not to create or drop the database itself.
您不能指定用户有权在数据库中创建或删除表，但不能有权创建或删除数据库本身。????
您不能指定用户有权在数据库中创建或删除表格，而不是创建或删除数据库本身。????



Without this privilege, the slave cannot request updates that have been made to
databases on the master server.
没有此特权，则从服务器不能请求主服务器数据库上已经完成的更新。
没有此特权，则从服务器不能请求对主服务器上的数据库进行更新。???




Enables control over client connections not permitted to non-SUPER accounts:
允许控制不被允许的的客户端连接到非 `SUPER` 账户 ???



A user with this privilege can specify any account in the DEFINER attribute of a
view or stored program.
拥有此权限的用户可以在视图或存储过程 `DEFINER` 的属性中指定任何账号



Privileges granted for the mysql database itself can be used to change passwords
and other access privilege information.
授予 `mysql` 数据库本身的特权可以用来更改密码和其他访问特权信息。



The FILE privilege can be abused to read into a database table any files that
the MySQL server can read on the server host.




For an account to be able to grant the PROXY privilege to other accounts, it
must have a row in the proxies_priv table with With_grant set to 1 and
Proxied_host and Proxied_user set to indicate the account or accounts for which
the privilege can be granted.
??? set to indicate the account 什么语法和意思



The server performs matching of host values in account names against the client
host using the value returned by the system DNS resolver for the client host
name or IP address.



The server accepts the connection only if the Host and User columns in some user
table row match the client host name and user name, the client supplies the
password specified in that row, and the account_locked value is 'N'.



Nonblank authentication_string values in the user table represent encrypted
passwords. MySQL does not store passwords in cleartext form for anyone to see.
Rather, the password supplied by a user who is attempting to connect is
encrypted (using the password hashing method implemented by the account
authentication plugin). The encrypted password then is used during the
connection process when checking whether the password is correct. This is done
without the encrypted password ever traveling over the connection. 
最后一句
This is done without the encrypted password ever traveling over the connection.
???



the client should call the mysql_options() C API function with the
MYSQL_SET_CHARSET_NAME option and appropriate character set name as arguments.
???客户端应该使用 `MYSQL_SET_CHARSET_NAME` 选项调用 C 语言的 API 函数
`mysql_options()` 并使用合适的字符集名作为参数
???客户端应该使用 `MYSQL_SET_CHARSET_NAME` 选项和合适的字符集名作为参数调用 C 语
言的 API 函数




Resource-use counting takes place when any account has a nonzero limit placed on
its use of any of the resources.
当任何帐户使用的任何资源是非零限制时，就会发生资源使用计数。




an edge case can occur if the account currently has open the maximum number of
connections permitted to it




Pluggable authentication makes it possible for clients to connect to the MySQL
server with credentials that are appropriate for authentication methods that
store credentials elsewhere than in the `mysql.user` table. 




It is also possible to connect using encryption from within an SSH connection to
the MySQL server host.  



The files need not have been generated automatically;




clients require an encrypted connection, and also perform verification against
the server CA certificate and against the server host name in its certificate.




They can be enabled at startup and inspected but not set at runtime.




Column, index, stored routine, event, and resource group names are not
case-sensitive on any platform, nor are column aliases.
列别名也不区分？？？
