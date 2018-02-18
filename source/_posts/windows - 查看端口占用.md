---
title: windows - 查看端口占用
categories:
  - Windows
tags:
  - Windows
---

```dos
> netstat -ano | findstr "5037"
  TCP    127.0.0.1:5037         0.0.0.0:0              LISTENING       7712

> tasklist|findstr "7712"
adb.exe                       7712 Console                    1      9,556 K

> taskkill /f /pid 7712
成功: 已终止 PID 为 7712 的进程。
```

```dos
> adb start-server
* daemon not running. starting it now on port 5037 *
* daemon started successfully *

```
