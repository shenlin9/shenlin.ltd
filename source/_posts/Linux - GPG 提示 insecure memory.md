---
title: Linux - GPG 提示 insecure memory
categories:
  - Linux
tags:
  - GPG
---

GnuPG 为了防止其他进程读取内存会尝试锁定内存，但因为某些儿原因（如平台
不支持内存锁定）没能成功锁定，所以就提示"使用了不安全的内存"

<!--more-->

## Why is GnuPG warning me about using insecure memory?

GnuPG tries to lock memory so that no other process can see it and so
that the memory will not be written to swap.  If for some reason it’s
not able to do this (for instance, certain platforms don’t support
this kind of memory locking), GnuPG will warn you that it’s using
insecure memory.

While it’s almost always better to use secure memory, it’s not
necessarily a bad thing to use insecure memory.  If you own the
machine and you’re confident it’s not harboring malware, then this
warning can probably be ignored.
