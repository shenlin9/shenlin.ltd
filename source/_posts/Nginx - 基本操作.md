---
title: Nginx - 基本操作
categories:
  - Nginx
tags:
  - Nginx
---

Nginx

<!--more-->

```bash
$ sudo nginx -s <signal>
```
* signal 包括 
    * stop - 快速关闭
    * quit - 优雅关闭 (等待 worker 线程完成处理)
    * reload - 重载配置文件
    * reopen - 重新打开日志文件

```bash

```
