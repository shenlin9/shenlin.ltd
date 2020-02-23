---
title: Linux - 文件种类 - file type
categories:
- Linux
tags:
- Linux
- file type
date: 2020-02-21 19:31:49
---

Linux - 文件种类 - file type

<!--more-->

## 文件类型

Linux 把一切都看作是文件，如果有东西不是文件，那它只能是进程。

Linux 文件系统最显著的特点，就是各种不同的文件之间的区别很小。

Linux 文件类型包含 3 种：
* Ordinary / General Files 普通文件，只包含数据
* Directory Files 目录文件，包含其他目录和文件
* Device Files 设备文件，用于表示所有的硬件设备

一个 Linux 文件不包含文件结束标记和文件名

## Ordinary Files

这是文件的传统定义。

可以把任何你想要的东西放进这种类型的文件中。它包括数据、文档、源代码、对象和可执
行代码等。

像 cat、ls、touch 等命令也是普通文件。

普通文件最常见的类型是文本文件。

## Directory Files

目录文件包含了它下面的子目录和文件的信息，

文件信息包括文件名和 inode (identification number)

## Device Files

Linux 把物理设备也看作文件，包括蓝牙、打印机、硬盘等

设备文件的特殊之处在于，任何指向此文件的输出都将反映到其对应的物理设备上。

## ls 命令

    Symbol      Meaning
    --------------------------
    + -         Regular file
    d           Directory
    l           Link
    c           Special file
    s           Socket
    p           Named pipe
    b           Block device
