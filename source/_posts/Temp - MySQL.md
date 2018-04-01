---
title: MySQL
categories: 
 - MySQL
tags: 
 - MySQL
---

MySQL

<!--more-->

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
