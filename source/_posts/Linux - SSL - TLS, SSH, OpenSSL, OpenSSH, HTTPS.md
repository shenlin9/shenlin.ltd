---
title: Linux - SSL - TLS, SSH, OpenSSL, OpenSSH, HTTPS
categories:
  - Linux
tags:
  - SSL
  - TLS
  - SSH
  - OpenSSL
  - OpenSSH
  - HTTPS
---

SSR,SSL,SSH

<!--more-->

## SS

SS 即为 shadowsocks，也就是大家常说的SOCKS代理，安卓版的中文名叫：影梭。

SOCKS 代理与其他类型的代理不同，它只是简单地传递数据包，而并不关心是何种应用协议
，既可以是 HTTP 请求，所以 SOCKS 代理服务器比其他类型的代理服务器速度要快得多。

SOCKS 代理又分为 SOCKS4 和 SOCKS5，二者不同的是 SOCKS4 代理只支持 TCP 协议（即传
输控制协议），而 SOCKS5 代理则既支持 TCP 协议又支持 UDP 协议（即用户数据包协议）
，还支持各种身份验证机制、服务器端域名解析等。

## SSL

Secure Sockets Layer 安全套接字层

NetScape 公司上世纪 90 年代中期设计，用于解决 HTTP 协议的明文传输问题。

SSL 指的是建立一条加密链路以加密通讯，其实这个链路中传输的内容是没有被 SSL 加密的。???

SSL 介于传输层（TCP）和应用层（HTTP,FTP）之间

## TLS

Transport Layer Security 传输层安全协议

SSL 因为应用广泛，已经成为互联网上的事实标准。

1999 年 IETF 把 SSL 标准化，标准化之后的名称改为 TLS。

两者常被并列称呼（SSL/TLS），因为这两者可以视作同一个东西的不同阶段。

## SSL/TLS 版本

在 SSL/TLS 家族中有5种协议：SSL v2, SSL v3, TLS v1.0, TLS v1.1, TLS v1.2。
( Shawn注: TLS v1.3还在draft阶段)

* SSL v2不安全，坚决不能用。( Shawn注: OpenSSL和GnuTLS当前的版本(2014.12.2)
不支持SSL v2)

* SSL v3老而且过时，她缺乏一些密钥特性，你不应该使用她除非有特别好的理
由。( Shawn注: POODLE漏洞的出现彻底的废掉了SSLv3，之前很多地方支持
SSLv3的原因是兼容性问题，GnuTLS 3.4中将默认不支持SSLv3)

* TLS v1.0在很大程度上是安全的，至少没有曝光重大的安全漏洞。

* TLS v1.1和TLS v1.2没有著名的安全漏洞曝光。( Shawn注: 由于Edward
Snowden曝光的内容有关于NSA“今天记录，明天解密”的故事，所以大量的自由
软件社区和暗网使者们在过去1年中转向了TLS v1.2的PFS)

* TLS v1.2应该成为你的主要协议。这个版本有巨大的优势是因为她有前面的版本
没有的特性。如果你的服务器平台不支持TLS v1.2，做个升级计划吧。如果你的
服务提供商不支持TLS v1.2，要求他们升级。

对于那些老的客户端，你还是需要继续支持TLS v1.0和TLS v1.1。对于临时的解
决方案，这些协议对于大多WEB站点依然被认为是安全。

## HTTPS

    HTTP over SSL 或 HTTP over TLS

## OpenSSL

https://www.openssl.org/

OpenSSL 是 TLS/SSL 协议的实现，是用于 TLS/SSL 协议的的工具包。

OpenSSL 整个软件包大概可以分成三个主要的功能部分

    密码算法库
    SSL 协议库
    应用程序

OpenSSL 是 apache-style 许可，意味着可以自由地获取和使用它，用于商业和非商业目的。

## SSH

SSH 为 Secure Shell 的缩写，由 IETF 的网络工作小组（Network Working Group）所制定；

SSH 是一种网络协议，用于计算机之间的加密登录，1995年，由芬兰学者 Tatu Ylonen 设计

SSH 为创建在应用层和传输层基础上的安全协议。

## OpenSSH

http://www.openssh.com/

OpenSSH 由 OpenBSD 项目的部分人员开发，并且使用 bsd-style 的许可。

OpenSSH 是使用 SSH 协议远程登录的主要连接工具，是 SSH 协议的一种实现。

OpenSSH 对所有的通信进行加密，以消除窃听、连接劫持和其他攻击。

OpenSSH 还提供了大量的安全隧道功能、几种身份验证方法和复杂的配置选项。

OpenSSH 套件由以下工具组成：

    远程操作使用 ssh、scp 和 sftp。
    密钥管理使用 ssh-add，ssh-Key-keyscan，ssh-keygen。
    服务端由 sshd、sftp-server 和 ssh-agent 组成。

## SSL 和 SSH 区别

SSH 和 SSL 都用于网络的安全通信

设计 SSL 的最初目的是加密 web 会话，但现在的应用不止于此。

设计 SSH 的最初目的是代替明文传输的 telnet 和 FTP，但现在的应用不止于此。

SSL 可应用于 HTTP, POP3, SMTP, IMAP 等行为良好的 TCP 应用。参考 stunnel.org

SSH 主要用于与主机建立安全通道，一些儿 SSH 的实现依赖于 SSL 库，因为 SSH 和 SSL
使用了很多相同的加密算法，如 TripleDES ，但这并不意味着 SSH 是基于 SSL 的。

SSH 和 SSL 是两个不同的协议，它们之间也不相互通信，但是在实现相似的目标方面有一
些重叠。

SSH 比 SSL 应用更广泛

SSL 协议本身不提供任何功能，只包括握手协议和加密方法，用户需要自己驱动 SSL 完成
应用的实际功能。

SSH 本身做了很多有用的事情，可以让用户执行真正的工作。

SSH 应用的两个方面是控制台登录(替换telnet)和安全文件传输(替换ftp),

但应用也额外获得了一个安全隧道，允许通过SSH隧道让用户运行 HTTP、ftp、POP3 等。

如果没有来自应用程序的通信，SSL 什么也不做。

如果没有来自应用程序的通信，SSH 将在两个主机之间建立一个加密的通道，允许您通过一
个交互的登录 shell、文件传输完成真正的工作。

HTTPS 不是扩展了 SSL，而是使用 SSL 安全地进行 HTTP 传输。

## OpenSSL 和 OpenSSH 区别

OpenSSH是SSH协议的实现， 实现过程中，需要用到密钥交换算法，对称/非对称加密算法，
Mac算法，随机数算法。

OpenSSL提供两个库libssl和libcrypto，OpenSSH使用的是libcrypto中实现的上述算法。

许多公司出于安全，效率，硬件加速等考虑，将OpenSSH porting到自己系统之后，会用自
己实现的算法替换这个算法库，我确切知道的有微软和IBM。

作者：赵子晟
链接：https://www.zhihu.com/question/40175330/answer/85906260

