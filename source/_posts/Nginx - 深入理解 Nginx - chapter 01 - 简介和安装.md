---
title: Nginx - 深入理解 Nginx - chapter 01 - 简介和安装
categories: Nginx
tags: Nginx
---

Nginx 由 Igor Sysoev 使用 C 语言开发。

<!--more-->

## 安装要求

### 软件包

Nginx 的模块可根据需求来定制，完成 web 服务器最基本功能所需的模块：

查看 linux 内核版本
```bash
$ uname -a
```
* 内核需 2.6 版本以上，因 2.6 以上才支持 epoll

GCC
```bash
$ yum install -y gcc gcc-c++
```
* 使用源码编译安装 Nginx 时需要 gcc
* 使用 C++ 开发 Nginx 模块时需要 gcc-c++

PCRE
```bash
$ yum install -y pcre pcre-devel
```
* perl compatible regular expressions
* 配置文件 nginx.conf 中有时会使用正则表达式，需要调用 pcre 来解析
* pcre-devel 是编译 nginx 必须的，也是使用 pcre 二次开发必须的

zlib
```bash
$ yum install -y zlib zlib-devel
```
* 用于对 HTTP 内容做 gzip 格式的压缩

OpenSSL
```bash
$ yum install -y openssl openssl-devel
```
* 使用 SSL 协议或 SHA1、MD5 等散列函数

### 配置 linux 内核

linux 内核参数考虑的是最通用的场景，并不适合作为高并发 web 服务器，最通用的支持高并发请求的 TCP 网络参数设置：

```bash
$ vi /etc/sysctl.conf           # 内核参数文件

    fs.file-max = 999999                            # 设定一个进程可以同时打开的最大句柄数，此参数直接限制了最大并发连接数

    net.ipv4.tcp_tw_reuse = 1                       # 设定是否允许把处于 TIME-WAIT 状态的 SOCKET 重新用于新的 TCP 连接

    net.ipv4.tcp_keepalive_time = 600               # 当启用 keepalive 时，TCP 发送 keepalive 消息的频度，默认 2 小时

    net.ipv4.tcp_fin_timeout = 30                   # 表示当服务器主动关闭连接时，socket保持在 FIN-WAIT-2 状态的最大时间

    net.ipv4.tcp_max_tw_buckets = 5000              # 表示操作系统允许 TIME_WAIT 套接字数量的最大值，默认为 180000，过多的TIME_WAIT套接字会使Web服务器变慢。
                                                    # 如果超过这个数字，TIME_WAIT套接字将立刻被清除并打印警告信息。

    net.ipv4.tcp_max_syn.backlog = 1024             # 表示 TCP 三次握手建立阶段接收SYN请求队列的最大长度，默认为1024，
                                                    # 将其设置得大一些可以使出现Nginx繁忙来不及accept新连接的情况时，Linux不至于丢失客户端发起的连接请求。

    net.ipv4.ip_local_port_range = 1024 61000       # 定义了在 UDP 和 TCP 连接中本地端口的取值范围 （不包括连接的远端）

    net.ipv4.tcp_rmem = 4096 32768 262142           # 定义了TCP接收缓存（用于TCP接收滑动窗口）的最小值、默认值、最大值。

    net.ipv4.tcp_wmem = 4096 32768 262142           # 定义了TCP发送缓存（用于TCP发送滑动窗口）的最小值、默认值、最大值。

    net.ipv4.tcp_syncookies = 1                     # 该参数与性能无关，用于解决TCP的SYN攻击

    net.core.netdev_max_backlog = 8096              # 当网卡接收数据包的速度大于内核处理的速度时，会有一个队列保存这些数据包。这个参数表示该队列的最大值。

    net.core.rmem_default = 262144                  # 表示内核套接字接收缓存区默认的大小

    net.core.wmem_default = 262144                  # 表示内核套接字发送缓存区默认的大小 

    net.core.rmem_max = 2097152                     # 表示内核套接字接收缓存区的最大大小

    net.core.wmem_max = 2097152                     # 表示内核套接字发送缓存区的最大大小
```

```bash
./config
make
make install
```

### config 选项和参数

#### 路径相关选项

|参数|含义|默认值|
|---|---|---|
|--prefix=PATH|安装目录，会影响其他参数的相对目录|/usr/local/nginx|
|--sbin-path=PATH|可执行文件的相对路径|&lg;prefix&gt;/sbin/nginx|
|--conf-path=PATH|配置文件的相对路径|&lg;prefix&gt;/conf/nginx.conf|
|--error-log-path=PATH|核心错误日志文件的相对路径|&lg;prefix&gt;/logs/error.log|
|--http-log-path=PATH|访问日志文件的相对路径，每一个 HTTP 请求在访问结束后都会写入访问日志|&lg;prefix&gt;/logs/access.log|
|--pid-path=PATH|pid 文件的存放路径，pid 文件里保存着 master 进程 ID|&lg;prefix&gt;/logs/nginx.pid|
|--lock-path=PATH|lock 文件的存放路径|&lg;prefix&gt;/logs/nginx.lock|
|--builddir=PATH|执行 configure 时和编译时临时文件的存放目录，包括产生的 Makefile、目标文件、可执行文件等|&lt;nginx source path&gt;/objs|
|--with-perl-modules-path=PATH|perl modules 的路径，只有使用了第三方的 perl modules，才需要设置此路径||
|--with-perl=PATH|perl 执行文件路径，配置 nginx 需执行 perl 脚本才需要设置此路径||
|--http-client-body-temp-path=PATH|HTTP 请求的 request body 如果需要暂时保存则保存到此磁盘文件|&lt;prefix&gt;/client_body_temp|
|--http-proxy-temp-path=PATH|Nginx 作为反向代理服务器时，上游服务器的 reponse body如果需要暂时保存则保存到此磁盘文件|&lt;prefix&gt;/proxy_temp|
|--http-fastcgi-temp-path=PATH|fastcgi 使用的临时文件的存放目录|&lt;prefix&gt;/fastcgi_temp|
|--http-uwsgi-temp-path=PATH|uwsgi 使用的临时文件的存放目录|&lt;prefix&gt;/uwsgi_temp|
|--http-scgi-temp-path=PATH|scgi 使用的临时文件的存放目录|&lt;prefix&gt;/scgi_temp|

#### 编译相关选项

|参数|含义|默认值|
|---|---|---|
|--prefix=PATH|安装目录，会影响其他参数的相对目录|/usr/local/nginx|
