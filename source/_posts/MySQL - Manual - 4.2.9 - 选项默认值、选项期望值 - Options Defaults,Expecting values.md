---
title: MySQL - Manual - 4.2.9 - 选项默认值、选项期望值 - Options Defaults,Expecting values
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

选项默认值、选项期望值

<!--more-->

## 等号

按照管理，长格式选项和选项值之间使用 `=` :
```
shell> mysql --host=tonfisk --user=jon
```

但对于那些必须赋值但又没默认值的选项，`=` 可以省略：
```
shell> mysql --host tonfisk --user jon
```

有默认值的选项，要赋值时 `=` 不能省略，例如 `mysqld_safe` 的选项 `--log-error` 有
默认值 `/usr/local/mysql/var/tonfisk.err`，则
```
shell> mysqld_safe &
[1] 11699
shell> 080112 12:53:40 mysqld_safe Logging to '/usr/local/mysql/var/tonfisk.err'.
080112 12:53:40 mysqld_safe Starting mysqld daemon with databases from /usr/local/mysql/var

shell> mysqld_safe --log-error &
[1] 11699
shell> 080112 12:53:40 mysqld_safe Logging to '/usr/local/mysql/var/tonfisk.err'.
080112 12:53:40 mysqld_safe Starting mysqld daemon with databases from /usr/local/mysql/var
```
* `&` 号告诉 OS 让程序在后台运行，但会被 MySQL 忽略

如果想更改其默认值，必须使用 `=` 号
```
shell> mysqld_safe --log-error=my-errors &
[1] 31437
shell> 080111 22:54:15 mysqld_safe Logging to '/usr/local/mysql/var/my-errors.err'.
080111 22:54:15 mysqld_safe Starting mysqld daemon with databases from /usr/local/mysql/var
```

否则不使用 `=` 的话，会发现尝试启动时意外关闭了：
```
shell> mysqld_safe --log-error my-errors &
[1] 31357
shell> 080111 22:53:31 mysqld_safe Logging to '/usr/local/mysql/var/tonfisk.err'.
080111 22:53:32 mysqld_safe Starting mysqld daemon with databases from /usr/local/mysql/var
080111 22:53:34 mysqld_safe mysqld from pid file /usr/local/mysql/var/tonfisk.pid ended
[1]+ Done ./mysqld_safe --log-error my-errors
```

查看错误日志会发现参数错误：
```
shell> tail /usr/local/mysql/var/tonfisk.err
2013-09-24T15:36:22.278034Z 0 [ERROR] Too many arguments (first extra is 'my-errors').
2013-09-24T15:36:22.278059Z 0 [Note] Use --verbose --help to get a list of available options!
2013-09-24T15:36:22.278076Z 0 [ERROR] Aborting
2013-09-24T15:36:22.279704Z 0 [Note] InnoDB: Starting shutdown...
2013-09-24T15:36:23.777471Z 0 [Note] InnoDB: Shutdown completed; log sequence number 2319086
2013-09-24T15:36:23.780134Z 0 [Note] mysqld: Shutdown complete
```

同样的问题也会出现在选项文件里，如
```
[mysql]
host
user
```
则 mysql 解析选项文件时会认为：
```
shell> mysql --host --user
// 或者
shell> mysql --host=--user
```
会提示错误 `ERROR 2005 (HY000): Unknown MySQL server host '--user' (1)`

或者这样
```
[mysql]
user jon
```
也提示错误
```
shell> mysql
mysql: unknown option '--user jon'
```
