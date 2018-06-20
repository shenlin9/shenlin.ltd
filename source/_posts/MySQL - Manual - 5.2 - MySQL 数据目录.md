---
title: MySQL - Manual - 5.2 - MySQL 数据目录
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 数据目录的位置是可以通过设置更改的，例如 Data 目录可以通过选项 `--datadir` 

来更改。

对于一个安装好的 MySQL，可以通过检测服务器设置来判断各目录是否被移动了。

<!--more-->

## Data

子目录

### mysql

mysql 目录对应 mysql 数据库，mysql 数据库包含了 MySQL 服务器运行时所需要的信息。

### performance_schema

performance_schema 目录对应于性能模式，此模式提供用于在运行时检查服务器内部执行
情况的信息

### sys

sys 目录对应于 sys 模式，该模式提供一组对象，以帮助更容易地解释性能模式信息

### ndbinfo

ndbinfo 目录对应于 ndbinfo 数据库，该数据库存储了 NDB Cluster 特定的信息(仅用于
构建包含 NDB Cluster 的安装)。

### 其他子目录

Data 目录下的其他子目录分别对应同名的数据库

另外，虽然 INFORMATION_SCHEMA 是一个标准数据库，但是它的实现没有使用相应的数据库
目录。

## 其他目录

MySQL 服务器日志文件目录

InnoDB 存储引擎的表空间和日志文件

默认和自动产生的证书、密钥文件

服务器进程 ID 文件（当服务器运行时）


