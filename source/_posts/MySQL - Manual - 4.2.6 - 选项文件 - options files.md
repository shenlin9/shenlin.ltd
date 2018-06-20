---
title: MySQL - Manual - 4.2.6 - 选项文件 - options files
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

大部分的 MySQL 程序都可以在启动时自动读取选项文件（配置文件），选项文件提供了一
个简便的方法来自动载入常用选项，避免每次都要在命令行输入。

<!--more-->

判断 MySQL 程序是否读取选项文件，可以使用 `--help` 选项，会指出当前命令会读取哪
个选项文件的哪个选项组。

如果 MySQL 程序启动时使用了选项 `--no-defaults`，则只读取 `.mylogin.cnf` 配置文
件，不再读取其他选项文件

## 文件格式

选项文件都是普通的文件文件，只有 `.mylogin.cnf` 例外，因为包含了登录路径选项，所
以是个加密文件，由 `mysql_config_editor` 应用创建.
Section 4.6.6, “mysql_config_editor — MySQL Configuration Utility”

## login path

登录路径 (login path) 是一个选项组，里面包含若干规定好的特定选项，客户端程序通过
`--login-path` 选项指定读取哪个登录路径，特定的选项包括：host, user, password,
port, socket

要指定另一个登录路径的文件名，可以通过设置环境变量 `MYSQL_TEST_LOGIN_FILE`，此
环境变量可以被测试应用 `mysql-test-run.pl` 使用，也可以被 `mysql_config_editor`
、`mysql`、`mysqladmin` 等程序识别

## Unix 平台

Unix 平台为了安全起见会忽略那些全域可写的配置文件。

Unix 平台和类 Unix 平台按下列顺序读取选项文件：

`/etc/my.cnf`           全局选项
`/etc/mysql/my.cnf`     全局选项
`SYSCONFDIR/my.cnf`     全局选项，SYSCONFDIR 表示 MySQL 被 CMake 构建时使用的
                        SYSCONFDIR 选项
`$MYSQL_HOME/my.cnf`    服务器端选项，`$MYSQL_HOME` 是个环境变量，值是 `my.cnf`
                        所在的目录，如果 `$MYSQL_HOME` 没有设置值，则如果启动时
                        使用了 `mysqld_safe` ，则把此值设置为 `BASEDIR`
                        MYSQL_HOME is an environment variable containing the
                        path to the directory in which the server-specific
                        my.cnf file resides. If MYSQL_HOME is not set and you
                        start the server using the mysqld_safe program,
                        mysqld_safe sets it to BASEDIR, the MySQL base
                        installation directory.
`defaults-extra-file`   使用 `--defaults-extra-file` 选项指定的文件
`~/.my.cnf`             针对用户的选项
`~/.mylogin.cnf`        针对用户的登录路径选项(clients only)

`DATADIR` 通常指向 `/usr/local/mysql/data`，这个值是 MySQL 构建时的数据目录，不
是使用 `mysqld` 启动时使用选项 `--datadir` 指定的目录，服务器在处理任何选项前会
先查找选项文件，但在在运行时使用 `--datadir` 对查找选项文件毫无影响

如果同一个选项被多次指定不同的值，则最后一次的指定值优先，只有一个例外：对于
`mysqld` ，`--user` 选项则是第一个指定的值优先，是为安全考虑，为了防止选项文件中
的用户名覆盖掉命令行中指定的用户名。

## 选项文件中的语法

选项文件中只能使用长格式选项 ???

选项的语法在选项文件中和命令行中不同：

1. 选项在选项文件中要省略前面的 `--`，即 `opt_name` 等价于 `--opt_name`，
   `opt_name=value` 等价于 `--opt_name=value`
2. `=` 前后可以有空格，这在命令行中是不正确的
3. 选项名和选项值的前后的空格会被自动删除
4. 可选的值可以包含在单引号或双引号中，如果值包含一个注释字符，这是很有用的。???
5. 一行只写一个选项
6. 空行被忽略
7. 注释以 `#` 或 `;` 开头，`#` 注释还可以从一行的中间开始
8. `[group]` 选项组，组名不分大小写
9. 转义序列：`\s \t \n \b \n \r \\`，利用此规则写 windows 路径：
```
basedir="C:\Program Files\MySQL\MySQL Server 5.7"
basedir="C:\\Program Files\\MySQL\\MySQL Server 5.7"
basedir="C:/Program Files/MySQL/MySQL Server 5.7"
basedir=C:\\Program\sFiles\\MySQL\\MySQL\sServer\s5.7
```
10. 如果选项组名和程序名相同，则此选项组自动应用选项到同名的程序，且只能应用到同
    名的程序，如选项组`[mysqld]` 自动应用到程序 `mysqld`
11. `[client]` 选项组将自动被所有客户端程序读取(不包括 mysqld)，因此放入这里的选
    项必须是所有客户端程序都可以识别的，否则将会显示错误后退出。合适的用法例如存
    放连接密码，但必须保证此选项文件只有你自己有权限查看，以防泄露密码。
12. 选项组的顺序：先列出最通用的选项组如 `[client]`，然后再列针对特定程序的选项
    组如 `[mysqldump]`，因为后列出的同名选项会覆盖先列出的同名选项。
13. 选项组还可以指定只针对某个具体版本，例如
```
[mysqld-5.7]
sql_mode=TRADITIONAL
```
14. 可以使用 `!include` 和 `!includedir` 来包含其他的选项文件或选项目录，包含
    目录时，MySQL无法保证目录中选项文件的读取顺序，且目录中的选项文件后缀，在
    unix 系统中为 `.cnf`，windows 系统中为 `.cnf` 或 `.ini`
```
!include /home/mydir/myopt.cnf
!includedir /home/mydir
```

全局选项文件举例
```
[client]
port=3306
socket=/tmp/mysql.sock

[mysqld]
port=3306
socket=/tmp/mysql.sock
key_buffer_size=16M
max_allowed_packet=8M

[mysqldump]
quick
```

用户选项文件举例
```
[client]
# The following password will be sent to all standard MySQL clients
password="my password"

[mysql]
no-auto-rehash
connect_timeout=2
```

