---
title: PHP - mysqld, libmysql
categories:
  - PHP
tags:
  - mysqld
  - libmysql
---

PHP configure 选项

<!--more-->

## 什么是mysqlnd？

mysqldnd（MySQL native driver）是由PHP源码提供的mysql驱动连接代码。它的目的是代替旧的libmysql驱动。

传统的安装php的方式中，我们在编译PHP时，一般指定以下几项:

--with-mysql=/usr/local/mysql \
--with-mysqli=/usr/local/mysql/bin/mysql_config \
--with-pdo-mysql=/usr/local/mysql/bin/mysql_config \

这实际上就是使用了MySQL官方自带的libmysql驱动, 这是比较老的驱动, PHP 5.3开始已经不建议使用它了, 而建议使用mysqlnd。

PDO与mysqlnd, libmysql又是何种关系？
PDO是一个应用层抽象类，底层和MySQL server连接交互需要MySQL驱动的支持。也就是说无论你使用了何种驱动，都可以使用PDO。
PDO是提供了PHP应用程序层API接口，而mysqlnd、libmysql则负责与MySQL server进行网络协议交互(它并不提供php应用程序层API功能)。

## 为什么使用mysqlnd驱动？

1. 传统的PHP访问MySQL数据库，是通过MySQL数据库的libmysql client库，这个libmysql client是用C/C++编写的，虽然一直以来PHP通过libmysql访问数据库性能也一直很好，但是却无法利用PHP本身的很多特性。

mysqlnd提供了和Zend引擎高度的集成性，更加快速的执行速度，更少的内存消耗，利用了PHP的Stream API，以及客户段缓存机制。由于mysqlnd是透过Zend引擎，因此提供更多高级特性，以及有效利用Zend进行加速，libmysql是直接访问数据库的，而mysqlnd是通过Zend访问数据库。

2. libmysql驱动是由MySQL AB公司(现在是oracle公司)编写, 并按MySQL license许可协议发布，所以在PHP中默认是被禁用的。而mysqlnd是由php官方开发的驱动,以php license许可协议发布，故就规避了许可协议和版权的问题

3. mysqlnd内置于PHP源代码,故你在编译安装php时就不需要预先安装MySQL server也可以提供MySQL client API (mysql_connect、pdo、mysqli)，这将减化一些工作量

4. 一些新的或增强的功能

增强的持久连接
引入特有的函数mysqli_fetch_all()
引入一些性能统计函数 mysqli_get_cache_stats(), mysqli_get_client_stats(), mysqli_get_connection_stats(),上述函数,可很容易分析mysql查询的性能瓶颈
SSL支持(从php 5.3.3开始有效)
压缩协议支持
命名管道支持(php 5.4.0开始有效)

## 怎么安装mysqlnd驱动？

编译php时,修改以下几个项参数即可，提示: 如果使用mysqlnd,并不需要预先安装mysql

--with-mysql=mysqlnd \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \

如果在phpinfo输出的mysql项中发现client API Version  : mysqlnd, 说明mysqlnd驱动安装成功.

