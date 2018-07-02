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
