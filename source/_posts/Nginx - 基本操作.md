---
title: Nginx - 基本操作
categories:
  - Nginx
tags:
  - Nginx
---

Nginx

<!--more-->

## 管理命令

```bash
$ sudo nginx -s <signal>
```
* signal 包括 
    * stop - 快速关闭
    * quit - 优雅关闭 (等待 worker 线程完成处理)
    * reload - 重载配置文件
    * reopen - 重新打开日志文件

## 指令

### location

```
location [modifier] path
```

匹配顺序：精确匹配 -> 优先匹配 -> 按顺序进行正则匹配 -> 前缀匹配

??? 顺序正则匹配，按配置文件里从上到下还是从下到上？

modifier 修饰符类型和举例
```bash
# = 表示精确匹配
location = /match {
  return 200 'Exact match';
}

# ^~ 表示优先匹配
location ^~ /match0 {
  return 200 'Preferential match';
}

# ~* 表示大小写 不 敏感的正则匹配
location ~* /match[0-9] {
  return 200 'Case insensitive regex match';
}

# ~ 表示大小写敏感的正则匹配
location ~ /MATCH[0-9] {
  return 200 'Case sensitive regex match';
}

# 没有修饰符，表示前缀匹配，则路径被视为前缀，其后可以跟随任何东西
location /match {
  return 200 'Prefix match: matches everything that starting with /match';
}
```
