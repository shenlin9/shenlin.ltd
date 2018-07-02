---
title: MySQL - Manual - 5.4.2 - 错误日志 - Error Log
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

本节讨论如何配置 MySQL 服务器，以便将诊断信息记录到错误日志中.

<!--more-->

错误日志记录了 mysqld 启动和关闭的时间，也记录了启动、关闭和运行时时的错误、警告
、注意事项等，例如 mysqld 注意到一个表需要检查和修复，则会自动写入此消息到日志里。

一些儿 OS 平台上，mysqld 非正常退出时会有堆栈跟踪写入错误日志，可用于判断 mysqld
的退出节点。

如果 mysqld_safe 用来启动 mysqld 则可能会将消息写入错误日志，例如 mysqld_safe 注
意到了 mysql 非正常退出了，则它会重新启动 mysqld，然后写入一条日志：mysqld
restarted

下面章节中的讨论，“console” means stderr, the standard error output. 

服务器解读选项时，在不同的 OS 平台上，写入错误日志的地方不同，因此要注意平台。

## 5.4.2.1 在 Windows 上的错误日志

略

## 5.4.2.2 在 Unix 和类 Unix 上的错误日志

mysqld 使用选项 `--log-error` 决定是否把错误日志写入文件或输出到标准错误输出：

    如果没有选项 `--log-error`，则 mysqld 把错误输出到标准错误输出

    如果设置了选项 `--log-error` 但没有为其指定文件名，则 mysqld 把错误日志输出
    到数据目录的 host_name.err 文件

    如果设置了选项 `--log-error` 且有选项值为文件名，则 mysqld 把错误日志输出到
    此文件，如果文件的后缀不是 `.err`，则会被加上后缀 `.err`，如果文件路径是相对
    路径，则是表示位于数据目录下

    如果在选项文件的 `[mysqld]`, `[server]`, `[mysqld_safe]` 章节中配置了
    `--log-error`，则 mysqld_safe 会把选项传递给 mysqld

对于 Yum 或 APT 软件包安装来说，在服务器的配置文件里使用选项
`log-error=/var/log/mysqld.log` 在 `/var/log` 目录下配置一个错误日志文件是很常见
的，如果没有指定文件名，则使用数据目录下的 `host_name.err` 文件。

如果 MySQL 服务器把错误日志输出到标准错误输出，则系统变量 log_error 被设置为
stderr，否则若 MySQL 服务器把错误日志输出到文件，则系统变量 log_error 被设置文件
名。

??? 输出到表，则 log_error 的值

## 5.4.2.3 把错误日志写入系统日志

系统日志，在 windows 上即事件日志，在 Unix 和类 Unix 系统为 syslog。

可以通过启用系统变量 log_syslog 把 mysqld 的错误日志写入系统日志（windows 系统默
认是启用的）,启用后再使用下列的系统变量进行更多的控制：

    log_syslog_facility: syslog 的缺省 facility 是 daemon，可以通过此变量更改

    log_syslog_include_pid: 是否在 syslog 输出的每一行中包含服务器进程 ID

    log_syslog_tag: 在 syslog 消息中的服务器标识 mysqld 后面添加连字符和由此选项定义
    的标签，

把日志写入到到系统日志可能还需要额外的系统配置，具体的系统配置需查看系统手册。

在 Unix 和类 Unix 系统上，还可以使用 mysqld_safe 把日志写入系统日志，因为
mysqld_safe 会捕获服务器错误输出并传递给 syslog，但已不推荐使用，推荐使用上述的
系统变量。

mysqld_safe 有 3 个和错误日志有关的选项：
    `--syslog`,
    `--skip-syslog`,
    `--log-error`，

不使用任何选项或使用 `--skip-syslog` 时都是使用默认的日志文件。

明确的指定错误日志文件可以使用 `--log-error=file_name`

指定写入到系统日志可以使用 `--syslog`

对于输出到 syslog 的日志，可以在标识符 `mysqld` 后面加上一个连字符和标签，使用
`--syslog-tag=tag_val`

## 5.4.2.4 过滤错误日志

系统变量 `log_error_verbosity` 控制服务器写入的错误日志的消息种类，取值及含义:

    1   errors only
    2   errors and warnings
    3   errors, warnings, and notes

默认值是 3，如果大于 2，则会记录中止链接和拒绝访问错误

具体参考 Section B.5.2.10, “Communication Errors and Aborted Connections”

## 5.4.2.5 错误日志消息格式

错误日志里的 ID 是 mysqld 中负责写入消息的线程的 ID，普通查询日志和慢查询日志中
的 ID 是连接线程的 ID

log_timestamps 系统变量控制日志消息（包括了错误日志、普通查询日志、慢查询日志）
中的时间戳，允许的值为 UTC（默认） 和 SYSTEM（系统本地时区）

## 5.4.2.6 错误日志文件刷新和重命名

如果使用 `FLUSH ERROR LOGS`, `FLUSH LOGS`, `mysqladmin flush-logs` 刷新日志，
MySQL 服务器将关闭并重新打开它正在写入的任何错误日志文件.

要重命名错误日志文件，可以先刷新日志，然后打开一个带有原始文件名的新文件。

例如错误日志文件名为 host_name.err
```bash
shell> mv host_name.err host_name.err-old
shell> mysqladmin flush-logs
shell> mv host_name.err-old backup-directory
```
* 如果 MySQL 服务器没有权限读写日志文件所在目录，则创建新的日志文件将失败
* 例如 linux 平台下日志文件可能位于 /var/log/mysqld.log，而 /var/log 目录为 root
  所有，mysqld 没有写无权限，相关参考：Section 5.4.7, “Server Log Maintenance”

如果服务器没有写入指定的错误日志文件，则在日志被刷新时则没有日志文件被重命名。
