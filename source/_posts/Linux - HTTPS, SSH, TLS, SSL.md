---
title: Linux - SSL, TLS, SSH, OpenSSL, OpenSSH, HTTPS
categories:
  - Linux
tags:
  - Linux
  - SSL
  - TLS
  - SSH
  - OpenSSL
  - OpenSSH
  - HTTPS
---

SSL,SSH,OpenSSL

<!--more-->

## 概念

SSL（Secure Sockets Layer）

    安全套接字层

    NetScape 公司上世纪 90 年代中期设计，用于解决 HTTP 协议的明文传输问题。

    SSL指的是建立一条加密链路以加密通讯，其实这个链路中传输的内容是没有被SSL加密的。???

    SSL 介于传输层（TCP）和应用层（HTTP,FTP）之间

TLS（Transport Layer Security）

    传输层安全协议

    SSL 因为应用广泛，已经成为互联网上的事实标准。1999 年 IETF 把 SSL 标准化，标准化之后的名称改为 TLS。

    两者常被并列称呼（SSL/TLS），因为这两者可以视作同一个东西的不同阶段。

SSL/TLS 版本

    在SSL/TLS家族中有5种协议：SSLv2, SSL v3, TLS v1.0, TLS v1.1, TLS v1.2。
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

    TLS v1.2应该成为你的主要协议。这个版本有巨大的优势是因为她有前面的版本
    没有的特性。如果你的服务器平台不支持TLS v1.2，做个升级计划吧。如果你的
    服务提供商不支持TLS v1.2，要求他们升级。

    对于那些老的客户端，你还是需要继续支持TLS v1.0和TLS v1.1。对于临时的解
    决方案，这些协议对于大多WEB站点依然被认为是安全。

HTTPS

    HTTP over SSL 或 HTTP over TLS

OpenSSL

    是SSL协议的实现，整个软件包大概可以分成三个主要的功能部分：密码算法库、SSL协议库以及应用程序。

SSH（Secure SHell）

    SSH只是加密的shell，最初是用来替代telnet的。

OpenSSH是SSH的实现

    OpenSSH依赖于OpenSSL，没有OpenSSL的话OpenSSH就编译不过去，也运行不了。

## 实践

查询是否安装ssh
```
rpm -qa | grep ssh
```

重启SSH服务
```
service sshd restart
```

查看是否启动22端口
```
netstat -antp | grep sshd
```

设置ssh服务开机启动
```
chkconfig sshd on 
```

## nginx 设置 https


```

```
