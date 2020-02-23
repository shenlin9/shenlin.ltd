---
title: Basis - AF, BF
categories:
  - Basis
tags:
  - Basis
  - AF
  - BF
---

AF 表示 ADDRESS FAMILY 地址族，PF 表示 PROTOCOL FAMILY 协议族，

<!--more-->

Winsock2.h 中
```
#define AF_INET 0
#define PF_INET AF_INET
```
所以在 windows 中 AF_INET 与 PF_INET 完全一样

而在 Unix/Linux 系统中，在不同的版本中这两者有微小差别 对于 BSD, 是 AF, 对于 POSIX 是 PF


