---
title: MySQL - Manual - 5.3 - MySQL 的系统数据库 mysql
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

mysql 是 MySQL 的系统数据库，包含了 MySQL 服务器运行时所需要的信息。

<!--more-->

mysql 数据库中的表都是系统表，除非另有说明，系统表一般使用的都是 MyISAM 存储引擎
，下面按类别介绍系统表。 

## 授权系统表

包含了授权信息：用户账号和权限

user            用户账号、全局权限和一些非权限列
db              数据库级的权限
table_priv      表级权限
columns_priv    列级权限
procs_priv      存储过程和函数权限
proxies_priv    代理用户权限

## 对象信息系统表

包含了存储程序、用户自定义函数、服务器端插件的信息

event           关于 Event Scheduler(事件调度器) 事件的信息。服务器启动过程中会
                载入此表中的事件，但使用了 `--skip-grant-table` 选项时则不载入。

func            关于用户自定义函数（UDFs）的信息。服务器启动过程中会载入此表中的
                自定义函数，但使用了 `--skip-grant-table` 选项时则不载入。

plugin          关于服务器端插件的信息。服务器启动过程中会载入此表中的插件，但使
                用了 `--skip-grant-table` 选项时则不载入。
                此表从 MySQL 5.7.6 开始使用 InnoDB 引擎，之前使用 MyISAM 引擎

proc            关于存储过程和函数的信息

## 日志系统表

服务器使用这些系统表进行日志记录

general_log     通用查询日志表

slow_log        慢速查询日志表

## 服务器端帮助信息系统表

这些系统表包含服务器端帮助信息

help_category   帮助信息类别

help_topic      帮助主题内容

help_keyword    与帮助主题相关的关键字

hep_relation    帮助关键字和主题之间的映射

这些表从 MySQL 5.7.5 开始使用 InnoDB 引擎，之前使用 MyISAM 引擎

## 时区系统表

这些系统表包含时区信息

time_zone                       时区 ID 和是否使用闰秒
time_zone_leap_second           闰秒发生时
time_zone_name                  时区 ID 和名称之间的映射
time_zone_transition            时区描述
time_zone_transition_types      时区描述

这些表从 MySQL 5.7.5 开始使用 InnoDB 引擎，之前使用 MyISAM 引擎

## 复制系统表

服务器使用这些系统表来支持复制

gtid_execute        存储GTID值的表

ndb_binlog_index    NDB Cluster 复制的二进制日志信息

slave_master_info   用于在从属服务器上存储复制信息
slave_relay_log_info用于在从属服务器上存储复制信息
slave_worker_info   用于在从属服务器上存储复制信息

这些表使用 InnoDB 引擎

## 优化器系统表

这些系统表供优化器使用

**innodb_index_stats, innodb_table_stats**

用于InnoDB持久优化器统计信息

**server_cost, engine_cost**

The optimizer cost model uses tables that contain cost estimate information
about operations that occur during query execution
优化器成本模型使用此表，此表包含的是在查询执行期间发生的操作的成本估算信息

server_cost contains optimizer cost estimates for general server operations.
server_cost 包含对一般服务器操作的优化器成本估算。

engine_cost contains estimates for operations specific to particular storage engines
engine_cost 包含特定于特定存储引擎的操作的估计

这些表都使用 InnoDB 引擎

## 其他系统表

其他系统表不属于前面的那些类别

**audit_log_filter, audit_log_user**

如果安装了 MySQL Enterprise Audit (企业审计)，这些表提供了审计日志的过滤器定义和
用户帐户的持久存储

**firewall_users, firewall_whitelist**

如果安装了 MySQL Enterprise Firewall (企业防火墙)，这些表提供了防火墙使用的信息
的持久存储

**servers**

此表供联合(FEDERATED)存储引擎使用

servers 表从 MySQL 5.7.6 开始使用 InnoDB 引擎，之前使用 MyISAM 引擎
