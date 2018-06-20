---
title: MySQL - Manual - 4.3.3 - MySQL 服务器启动脚本 - mysql.server
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

mysql.server

<!--more-->

## mysql.server

Unix 和类 Unix 系统的 MySQL 发行版包含一个叫 `mysql.server` 的脚本，它使用
`mysqld_safe` 来启动 MySQL 服务器 `mysqld`，它可用在使用 System V 风格的通过运行
目录中的特定文件来启动和停止服务的操作系统上如 Linux 或 Solaris，也被 macOS 的
MySQL 启动项使用。

`mysql.server` 是 MySQL 源码树中的内部脚本名，安装后的名字可能不同，例如变为
mysql 或 mysqld

在一些儿 Linux 平台，RPM 或 Debian 的 MySQL 安装包含有 systemd 用于管理 MySQL 服
务器的启动和停止，在这些平台上 `mysqld_safe` 和 `mysql.server` 则没有安装因为没
必要了。

## 使用 mysql.server 启动和停止服务器

```
shell> mysql.server start
shell> mysql.server stop
```
* mysql.server 将定位到 MySQL 安装目录并调用 mysqld_safe
* 若想要以指定用户运行服务器，则在 `/etc/my.cnf` 选项文件中的 `[mysqld]` 里增加
  `user=xxx`
* 如果二进制发行版的 MySQL 被安装在一个非标准位置，则很可能你必须在此文件调用
  mysqld_safe 之前先编辑 mysql.server 以修改路径到准确的位置。但以后 MySQL 升级
  的时候很可能会覆盖你修改的此文件，因此最好对文件做个备份。
