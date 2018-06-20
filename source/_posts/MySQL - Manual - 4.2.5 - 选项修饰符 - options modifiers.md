---
title: MySQL - Manual - 4.2.5 - 选项修饰符 - options modifiers
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

选项修饰符

<!--more-->

## --disable, --skip, =0, --enable, =1

有些儿选项可以控制行为的打开和关闭，为布尔类型的

这两个前缀和一个后缀可以达到同样的效果，即关闭选项

```
shell> mysql --disable-clolumn-names
shell> mysql --skip-clolumn-names
shell> mysql --clolumn-names=0
```

ON, OFF, TRUE, FALSE 也是正确的值，不区分大小写，下面的为打开选项

```
shell> mysql --enable-clolumn-names
shell> mysql --clolumn-names
shell> mysql --clolumn-names=1
```

## --loose

选项前加前缀 `--loose` 时，程序在遇到不认识的选项时则不会出错退出，而是给出警告

```
shell> mysql --loose-no-such-option
mysql: WARNING: unknown option '--loose-no-such-option'
```

## --maximum

`--maximum` 前缀只针对 `mysqld` 命令有效，用于限制客户端会话变量，例如：限制堆表
最大不能超过 32M

```
shell> mysqld --maximum-max_heap_table_size=32M
```

`--maximum` 前缀只能应用于有会话值的系统变量，如果应用于有全局值的系统变量，则会
发生错误，例如：

```
shell> mysqld --maximum-back_log=200
Maximum value of 'back_log' cannot be set
```
