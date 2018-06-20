---
title: MySQL - MySQL Windows 安装程序 - MySQL Installer 
categories: 
 - MySQL
tags: 
 - MySQL Windows
 - MySQL Installer
---

MySQL Installer

<!--more-->

## Windows 系统的 MySQL Installer

MySQL Installer 是一个独立的应用程序，旨在简化在 Windows 上安装和管理运行 MySQL
产品的复杂性.

虽然 MySQL Installer 是一个 32 位程序，但 32 位或 64 位的 MySQL 产品组件都可以安装

MySQL 分为社区版和商业版，MySQL Installer 也对应的分为社区版和商业版，下载地址也
不同：

    社区版
    http://dev.mysql.com/downloads/installer/

        Web 版 MySQL Installer：
            只包含 MySQL Installer 本身和配置文件，大小约为 2MB，
            要通过网络连接来下载安装用户选择的 MySQL 的产品组件，名字格式为：
            mysql-installer-community-web-VERSION.N.msi
                VERSION: MySQL 服务器版本号如 5.7
                N      : 包的序号，从 0 开始

        完全版 MySQL Installer：
            包含了 MySQL 的所有产品组件，包括服务器组件，大小超过 300MB
            mysql-installer-community-VERSION.N.msi
                VERSION: MySQL 服务器版本号如 5.7
                N      : 包的序号，从 0 开始

    商业版
    https://edelivery.oracle.com/

        商业版除了社区版的全部功能，还额外包括：
            Workbench SE/EE
            MySQL Enterprise Backup
            MySQL Enterprise Firewall

        MOS 账户：My Oracle Support (MOS)

MySQL Installer 支持 MySQL 以下产品：

    MySQL 服务器
    MySQL 应用程序
    MySQL 连接器
    文档和样例

### MySQL Installer 初始化设置

先安装 MySQL Installer 自身，配置文件会在安装过程中提取到硬盘里，然后执行菜单组
里的 MySQL Install 程序

#### MySQL Installer 的几个安装类型

开发人员默认：
    • MySQL Server (安装你下载 MySQL Installer 时选择的版本)
    • MySQL Shell
    • MySQL Router
    • MySQL Workbench
    • MySQL for Visual Studio
    • MySQL for Excel
    • MySQL Notifier
    • MySQL Connectors (.NET / Python / ODBC / Java / C / C++)
    • MySQL Utilities
    • MySQL Documentation
    • MySQL Samples and Examples

仅服务端：
    • MySQL Server (安装你下载 MySQL Installer 时选择的版本)，使用默认的安装路
    径和数据路径

仅客户端：
    • 和开发人员默认安装相似，但去除了服务端组件和那些典型的和服务端组件绑定的
    客户端组件，如 mysql,mysqladmin

完全安装:
    • 所有可用的组件

自定义安装，好处如下：
    • 按自己需要任意组合安装特定的产品和特性，如只安装 MySQL Workbench。
    • 自定义安装路径和数据路径
    • 可以获取从通常下载位置获取不到的产品和版本，如在预发布和 GA 之间的版本 

#### 路径冲突

### MySQL 服务器

MySQL Installer 可以在同一台主机上同时安装、配置、更新、管理多个各自独立的 MySQL
服务器实例，如 MySQL 5.6, MySQL 5.7, MySQL 8.0 在同一台主机。

MySQL Installer 不允许 MySQL 服务器升级主要版本号和次要版本号，但允许升级修订版
本号，如从 5.7.18 到 5.7.19

一台主机不能同时安装 MySQL 服务器的社区版本和商业版本

### MySQL 应用程序

MySQL Workbench
MySQL Shell
MySQL Router
MySQL for Visual Studio
MySQL for Excel
MySQL Notifier
MySQL Utilities.

### MySQL 连接器

MySQL Connector/Net
MySQL Connector/Python
MySQL Connector/Node.js
MySQL Connector/ODBC
MySQL Connector/J
MySQL Connector/C
MySQL Connector/C++

### 文档和样例

MySQL 参考手册（按版本，PDF格式）和 MySQL 数据库样本（按版本）
