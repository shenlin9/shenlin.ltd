---
title: MySQL - Manual - 4.3.2 - MySQL 服务器启动脚本 - mysqld_safe
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

mysqld_safe

<!--more-->

mysqld_safe 是 Unix 上启动 mysqld 的推荐方式，因为它具有一些儿安全特性，如：发生
错误时重启服务器，并将当时的运行信息记录到错误日志中.

对于一些儿 Linux 平台来说，RPM 和 Debian 的 MySQL 安装包提供了用于启动和关闭
MySQL 服务器的 systemd，这些平台因为没有必要所以就不会安装 mysqld_safe.
参见：Section 2.5.10, “Managing MySQL Server with systemd”.

mysqld_safe 尝试启动名为 mysqld 的可执行文件，如果要覆盖默认行为并明确指定要运行
的服务器名称，可以为 mysqld_safe 指定选项 `--mysqld` 或 `--mysqld-version`，还可
以使用`--ledir` 选项指示 mysqld_safe 应该在哪个目录里查找服务器

mysqld_safe 的很多选项和 mysqld 是一样的。

mysqld_safe 会把命令行上指定的未知选项传递给 mysqld，但是如果在选项文件的选项组
`[mysqld_safe]` 里指定的未知选项则不会传递。

mysqld_safe 会读取选项文件中的下列选项组： `[mysqld]`, `[server]`, `[mysqld_safe]` 
，为了向后兼容性，还会读取 `[safe_mysqld]`，但新写的选项文件应该写成
`[mysqld_safe]`

## mysqld_safe 选项

--user={user_name|user_id}      以用户名为 user_name 或用户 ID 为 user_id 的身份
                                运行 MySQL 服务器，注意这里的用户指的是操作系统
                                的登录账号，而不是 MySQL 授权表里的列表用户

--pid-file=file_name            服务器进程 mysqld 进程 ID 文件的路径，从 MySQL
                                5.7 到 5.7.17，mysqld_safe 有它自己的进程 ID 文
                                件，叫 `mysqld_safe.pid`，位于 MySQL 的数据目录

--basedir=dir_name              MySQL 安装目录的路径
--datadir=dir_name              数据目录路径

--ledir                         服务器文件所在的目录路径，如果 mysqld_safe 无法
                                找到服务器程序，使用此选项指明服务器程序所在的目
                                录，在  MySQL 5.7.17 版本中，此选项只能用于命令
                                行，不能用于选项文件，在使用 systemd 的平台上，
                                选项值可以从 MYSQLD_OPTS 中选择

--plugin-dir=dir_name           插件安装目录

--mysqld=prg_name               在 ledir 目录中的要启动的服务器程序名，如果使用
                                的是二进制发行版，数据目录位于 MySQL 目录之外则
                                需要此选项

--mysqld-version=suffix         服务器程序的后缀，例如设定
                                `--mysqld-version=debug` 则 mysqld_safe 将启动
                                位于 ledir 目录中的 mysqld-debug 程序

--mysqld-safe-log-timestamps    用于设置 mysqld_safe 输出的日志中时间戳格式，允
                                许的取值列表如下，取值不在列表则 mysqld_safe 记
                                录一个警告并使用 UTC 格式，从版本5.7.11 添加此选
                                项
                                UTC,utc
                                    默认值，和使用 --log_timestamps=UTC 效果相同
                                SYSTEM,system
                                    本地格式，和使用 --log_timestamps=SYSTEM 效果相同
                                HYPHEN,hyphen
                                    YY-MM-DD h:mm:ss
                                LEGACY,legacy
                                    YYMMDD hh:mm:ss

--skip-kill-mysqld              不尝试终止迷失的 mysqld 进程(stray mysqld
                                processes)，此选项只在 linux 平台工作

--core-file-size=size           mysqld 应该能创建的核心文件的大小，值被传递给
                                ulimit-c

--open-files-limit=count        mysqld 应该可以打开的文件数量，值被传递给
                                ulimit-n，必须以 root 身份启动 mysqld_safe 才能
                                使得此功能正常。

--no-defaults                   不读取任何选项文件，如果程序启动时因为从选项文件
                                中读取到了未知选项儿导致启动失败，则此选项可以阻
                                止未知选项被读取。在命令行上使用时必须是第一个选
                                项。

--defaults-file=file_name       只读取指定的选项文件，如果文件不存在或不可访问，
                                则服务器会出错并退出。如果给出的不是全路径而是相
                                对路径则解释为是相对于当前目录的路径。在命令行上
                                使用此选项时则必须是第一个选项，否则不会读取指定
                                的选项文件。

--defaults-extra-file=file_name 除了读取通常的选项文件之外，还读取指定的选项文件
                                ，如果文件不存在或不可访问，则服务器会出错并退出
                                。如果给出的不是全路径而是相对路径则解释为是相对
                                于当前目录的路径。在命令行上使用此选项时则必须是
                                第一个选项，否则不会读取指定文件。

--socket       	                用于监听的 unix 套接字文件

--port=port_num                 监听 TCP/IP 连接的端口号，除非数据库系统由 root
                                启动，否则端口号必须大于等于 1024

--skip-syslog  	                不要把错误信息写入系统日志（syslog），而是写入错
                                误日志文件

--syslog       	                把错误信息写入系统日志（syslog），当使用 syslog
                                时，文件 daemon.err 中的 facility/severity 会用
                                于所有日志信息

                                从 MySQL 5.7.5 开始已不推荐使用 `--syslog` 和
                                `--skip-syslog` 来控制 mysqld 的日志，应使用服务
                                器的系统变量 `log_syslog` 代替，如果要控制
                                facility，使用服务器的系统变量 `log_syslog_facility`

--syslog-tag=tag                设定写入 syslog 的消息的标记后缀。
                                当日志写入 syslog 时，mysqld 和 mysqld_safe 的日
                                志有各自的标识符分别是 mysqld 和 mysqld_safe，此
                                选项为标识符指定后缀时，即把标识符修改为
                                mysqld-tag 和 mysqld_safe-tag

                                从 MySQL 5.7.5 开始已不推荐使用此选项，推荐使用
                                服务器的系统变量 `log_syslog_tag` 代替

--log-error=file_name           把错误日志写入指定的文件

--malloc-lib=[lib_name]         用于内存分配的库，用来替代系统的 malloc 库。从
                                MySQL 5.7.15 起，选项值必须是下列目录之一：
                                        /usr/lib
                                        /usr/lib64
                                        /usr/lib/i386-linux-gnu
                                        /usr/lib/x86_64-linux-gnu

                                5.7.15 前的版本，任何库都可以使用，只要指定它的
                                目录路径即可。

                                5.7 版本中，为 Linux 提供的二进制 MySQL 发行版有
                                调用 tcmalloc 库的快捷方式，但在特定配置下，这种
                                调用方式不起作用，这时还是需要指定路径名来调用

                                从 MySQL 5.7.13 起，MySQL 发行版不再包含
                                tcmalloc 库

                                malloc-lib 选项通过修改 LD_PRELOAD 环境变量值来
                                影响动态链接，进而使加载器能够在 mysqld 运行时找
                                到内存分配库，具体情况是：
                                1. 没有设定 malloc-lib 选项，或者给出了选项但没
                                   有为其赋值，即 `--malloc-lib=`，则 LD_PRELOAD
                                   没有被修改，不会尝试使用 tcmalloc 库
                                2. 如果选项设定为 `--malloc-lib=tcmalloc`，则
                                   mysqld_safe 会在目录 `/usr/lib` 和 MySQL
                                   pkglibdir 位置寻找 tcmalloc 库。tcmalloc 库被
                                   找到后，其路径会被添加到 LD_PRELOAD 最前面以
                                   供 mysqld 使用。如果没有找到 tcmalloc 库，则
                                   mysqld_safe 以一个错误终止。
                                3. 如果选项设定为 `
                                   --malloc-lib=/path/to/some/library`，则此全路
                                   径会被添加到 LD_PRELOAD 最前面，如果全路径的
                                   指向不存在或不可读，则mysqld_safe 以一个错误
                                   终止。
                                4. mysqld_safe 为 LD_PRELOAD 添加路径时，总是把
                                   路径添加到现有值的最前面。
                                5. 使用 systemd 管理的服务器系统，mysqld_safe 不
                                   可用，可以通过在 `/ect/sysconfig/mysql` 中设
                                   定 LD_PRELOAD 的值来指示库的位置

                                linux 用户可以通过在 `my.cnf` 文件中添加下列配置
                                来使用包含在二进制包中的
                                `libtcmalloc_minimal.so` 库，这对于任何安装了
                                tcmalloc 库到 `/usr/lib` 目录的用户来说都是适用
                                的：
                                ```
                                [mysqld_safe]
                                malloc-lib=tcmalloc
                                ```

                                使用指定的 tcmalloc 库，用全路径：
                                ```
                                [mysqld_safe]
                                malloc-lib=/opt/lib/libtcmalloc_minimal.so
                                ```

--nice=priority                 使用 nice 程序设置服务器的调度优先级

--timezone=timezone             设置 TZ 时区环境变量为给定的值，时区的合法格式需
                                参考操作系统的文档

--help                          显示帮助信息


## 启动 mysqld

mysqld_safe 是脚本编写的，所以无论是二进制安装还是源码安装的 MySQL 通常来说都是
可以启动的，即使它们的安装位置稍有不同。

mysqld_safe 期待下列情况之一：

1. 当你从 MySQL 安装目录执行 mysqld_safe 时会遇到这种情况：对于二进制发行版，
   mysqld_safe 将在自己所在的目录下查找 bin 和 data 目录；对于源码发行版，
   mysqld_safe 将在自己所在的目录下查找 libexec 和 var 目录。

2. 如果经过上面的步骤，mysqld_safe 在相对目录下找不到服务器和数据库目录，则尝试
   使用全路径查找，典型的目录例如：/usr/local/libexec 和 /usr/local/var （真正的
   目录取决于 MySQL 编译时设定的值）

因为 mysqld_safe 是用相对目录查找服务器程序 mysqld 和数据库目录，因此二进制的
MySQL 可以安装到任何位置，启动 MySQL 服务器时只需要进入 MySQL 安装目录执行
mysqld_safe 即可，例如:
```
shell> cd mysql_installation_directory
shell> bin/mysqld_safe &
```

即使进入 MySQL 安装目录执行 mysqld_safe 失败，也可以通过选项 `--ledir` 和
`--datadir` 选项来指示服务器程序目录和数据库目录。

mysqld_safe 使用系统的 sleep 和 date 应用来确定每秒尝试几次来启动，如果这些应用
存在且每秒尝试 5 次以上，则每次尝试间隔 1 秒，这是为了在重复出现故障的情况下不占
用过多的 CPU 资源。

## 错误消息

当使用 mysqld_safe 启动 mysqld 时，mysqld_safe 接受来自自身的和 mysqld 的错误消
息、通知消息，并将其发送到同一个目的地。

mysqld_safe 有几个选项用于控制这些消息的目的地：

1. `--log-error=file_name` 把错误消息写入指定的文件

2. `--syslog` 把错误消息写入支持 logger 程序的操作系统的 syslog

3. `--skp-syslog` 不把错误消息写入 syslog，而是写入默认的日志文件（数据库目录下
   的 `host_name.err`），或者写入由选项 `--log-error` 指定的文件

4. 任何选项都没有给出的时候，默认缺省值是 `--skip-syslog`

当 mysqld_safe 写入消息时，通知会发送到日志目的地（syslog 或错误日志文件）和
stdout，错误则会发送到日志目的地和 stderr.

从 MySQL 5.7.5 起，用 mysqld_safe 控制 mysqld 日志已被弃用，替代它的是服务器本地
的 syslog 支持，具体参考：Section 5.4.2, “The Error Log”

