---
title: MySQL - Manual - 4.2.7 - 影响选项文件处理的命令行选项 - options affect options files
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

有些儿选项会影响如何处理选项文件，因此不能写在选项文件中，只能用在命令行上。

<!--more-->

## 选项位置

这些儿选项要正常发挥作用，必须位于其他选项之前。

也有下面几个例外：

1. `--print-defaults` 可以紧跟在这些选项后使用：`--defaults-file`,
   `--defaults-extra-file`, `--login-path`

2. Windows 上启动 MySQL 服务器时，如果同时使用了选项 `--defaults-file` 和
`--install`，则 `--install` 必须是第一个选项

##

