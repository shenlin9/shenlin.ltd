---
title: MySQL
categories: 
 - MySQL
tags: 
 - MySQL
---

MySQL

<!--more-->

## 连接和退出

有的 MySQL 安装允许匿名登录，则无需用户名
```sql
$ mysql
```

连接时指定用户
```sql
$ mysql -u root -p
```

连接时指定主机
```bash
$ mysql -h localhost -u root -p
```

连接时指定数据库
```bash
$ mysql -h localhost -u root -p test
```
* 数据库名区分大小写

查看 mysql 版本
```sql
SELECT VERSION();
```

退出 mysql
```sql
quit
```
* quit 后的冒号可省略

## 忘记 mysql 的登录用户名或密码

重启 mysqld ，禁用权限表，则所有人都可以连接 mysql
```bash
$ sudo service mysqld stop
$ sudo service mysqld start --skip-grant-tables
```

mysql的用户与密码都在系统表 `mysql.user` 里,只要修改相应的字段就可以了。
```sql
USE mysql；
UPDATE `user` SET `authentication_string`=PASSWORD('123456') WHERE `User`='user';
FLUSH PRIVILEGES;
```
* 123456是密码，user是用户，你如果要该用户名的话 ，注意where后面的条件就可以了，

## 修改列的定义

```sql
alter table wanjia change column addr addr varchar(10);
```
