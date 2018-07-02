---
title: MySQL - Manual - 6 - 安全 - Security
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

安全综述

<!--more-->

影响安全的一般因素包括：选择好的密码，不向用户授予不必要的权限，防止 SQL 注入和
数据损坏等，参见：Section 6.1, “General Security Issues”.

安装本身的安全性。数据文件、日志文件和安装的所有应用程序文件都应该受到保护，以确
保它们不被未经授权的部分读或写，参见：Section 2.10, “Postinstallation Setup and
Testing”.

数据库系统本身的访问控制和安全性，包括受权访问数据库、视图和存储程序的用户和数据库。
参考：Section 6.2, “The MySQL Access Privilege System”
      Section 6.3, “MySQL User Account Management”.

与安全相关的插件提供的特性
参考：Section 6.5, “Security Plugins”.


MySQL 和 OS 系统的网络安全性。安全与对每个用户的授权有关，但是您也可能希望限制
MySQL，以便它只能在MySQL服务器主机上本地可用，或者只在有限的其他主机上使用。
The security is related to the grants for individual users, but you may also
wish to restrict MySQL so that it is available only locally on the MySQL server
host, or to a limited set of other hosts.


确保您对数据库文件、配置和日志文件有足够和适当的备份。还要确保你有一个恢复的解决
方案并且测试你能够成功地从你的备份中恢复信息
参考： Chapter 7, Backup and Recovery.
