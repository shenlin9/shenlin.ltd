---
title: Basis - AF, BF
categories:
  - Basis
tags:
  - Basis
  - AF
  - BF
---

AF 表示ADDRESS FAMILY 地址族，PF 表示PROTOCOL FAMILY 协议族，

<!--more-->

Winsock2.h中
```
#define AF_INET 0
#define PF_INET AF_INET
```
所以在windows中AF_INET与PF_INET完全一样
 
而在Unix/Linux系统中，在不同的版本中这两者有微小差别 对于BSD,是AF,对于POSIX是PF


