---
title: MySQL - Manual - 4.2.8 - 使用选项设置程序变量 - Using Options to Set Program Variables
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

很多 MySQL 程序可以在运行时通过 `SET` 语句为内部变量赋值。

<!--more-->

例如，MySQL 有内部变量 `max_allowed_packet` 设定通讯时的最大缓冲，则其对应的选项
则为 `--max_allowed_packet`

```
shell> mysql --max_allowed_packet=16777216      // 字节
shell> mysql --max_allowed_packet=16M           //
```

选项可以使用没有歧义的前缀缩写，如：`max_allowed_packet` 可缩写为 `max_a`
```
shell> mysql --max_a=1000000
```
但不能缩写为 `max`
```
shell> mysql --max=1000000
mysql: ambiguous option '--max=1000000' (max_allowed_packet, max_join_size)
```
注意，使用前缀缩写可能存在的问题是：现在是明确的前缀缩写，以后可能不明确


Suffixes for specifying a value multiplier can be used when setting a variable at server startup, but not to
set the value with SET at runtime.
On the other hand, with SET, you can assign a variable's value using an expression, which is not true when you set a variable at server startup. 


在 MySQL 服务器启动时，可以使用乘法运算符设置变量值，可以使用 `SET` 和表达式为变量赋值，但是不能在运行时把 `SET` 和 乘法运
算符一起使用，可以在服务器启动时，

举例，在服务器启动时下面的例子中第一个语句是合法的，第二个语句不合法：
```
shell> mysql --max_allowed_packet=16M
shell> mysql --max_allowed_packet=16*1024*1024
```
相反，在运行时下面的例子中第二个语句是合法的，第一个语句不合法：
```
mysql> SET GLOBAL max_allowed_packet=16M;
mysql> SET GLOBAL max_allowed_packet=16*1024*1024;
```
