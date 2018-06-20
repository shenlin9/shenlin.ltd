---
title: MySQL - Manual - 4.2.4 - 选项规则 - options rules
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

在命令行上指定的程序选项遵循下列规则：

<!--more-->

1. 命令后给出选项

2. 选项前使用 `-` 还是 `--` 由选项的格式决定，短格式使用一个，长格式使用两个

3. 选项区分大小写，如 `-v` 一般为 `--verbose` 短格式，`-V` 为 `--version` 短格式

4. 有些儿选项后面带选项值，如 `-h localhost` 或 `--host=localhost`

5. 选项和选项值之间，长格式使用 `=` 分隔，如 `--host=localhost`，短格式可以不分
   隔直接连接或使用空格分隔，如 `-hlocalhost` 或 `-h localhost`，此条规则的唯一
   例外是密码选项，短格式不支持空格分隔，即不可以 `-p Passwd`，因为无法分清
   Passwd 是不是另一个无选项参数数据库名，必须 `-pPasswd` 这样

6. 选项名中的连接线和下划线是可以互相替换的，如 `--skip-grant-tables` 和
   `--skip_grant_tables` 是等价的

7. 选项值如果是数字值，可以使用不区分大小写的 K, M, G 作为后缀，例如：`shell>
   mysqladmin --count=1K --sleep=10 ping`

8. 选项值是文件名时，避免使用 shell 的元字符 `~`，因为很可能不会被正确解释.

9. 选项值中有空格时应该引起来，如：`shell> mysql -u root -p --execute="SELECT
   User, Host FROM mysql.user"`

10. 多条 SQL 语句作为选项值时，可以使用分号分隔，如：`shell> mysql -u root -p -e
    "SELECT VERSION();SELECT NOW()"`

