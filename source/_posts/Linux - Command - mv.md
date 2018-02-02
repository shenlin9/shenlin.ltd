---
title: Linux - Command - mv
categories:
  - Linux
  - Command
tags:
  - mv
---

mv

<!--more-->

移动所有文件到指定目录
```
$ mv  *  .[^.]*  target-dir
```
* `*` 匹配除隐藏文件外的所有文件
* `.[^.]*` 匹配隐藏文件，表示以 `.` 开头后面跟上不是 `.` 的任意字符
* `.*` 匹配目录 `.` 和 `..`
??? 确认 .* 和 *.* 和 cp rm 等命令
