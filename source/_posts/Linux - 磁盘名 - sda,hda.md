---
title: Linux - 磁盘名 - sda,hda
categories:
- Linux
tags:
- Linux
- sda
- hda
date: 2020-02-21 16:26:48
---

Linux - 磁盘名 - sda,hda

<!--more-->

## Linux

linux 的磁盘分区如下所示：
* sda1，sdb2
* hda2，hdb1
* fda1

由三部分组成：
* 磁盘类型
* 发现顺序
* 分区编号

### 磁盘类型

最初
fd 代表 floppy disk，hd 代表 IDE(也叫 PATA) 设备，sd 代表 SCSI 设备

后来
SATA 设备出现后，被直接归入了 sd 编号
USB 设备迅猛增长后，也归入了 sd 编号

所以现在 sd 这两个字符的含义：
* 可能是 SCSI 设备
* 可能是 SATA 设备
* 可能是 USB 设备

>>> ??? 待确认
>>> 也有说标准输入输出和错误输出就是现在的fd0，fd1和fd2。
>>> cd /dev
>>> ls -l stdin stdout stderr

>>> ??? 待确认
>>> fd 不是 fda 而是 fd0
>>> The first floppy drive is named /dev/fd0.
>>> The second floppy drive is named /dev/fd1.
>>> The first SCSI CD-ROM is named /dev/scd0, also known as /dev/sr0.

### 发现顺序

后面的 a、b 则是系统发现磁盘的顺序
第一块磁盘就是 a，到 z 后，则再从 Aa，Ab 这样排列
即 linux 的磁盘名是使用字母顺序的
* hda – stands for an PATA (Parallel ATA) Hard-disk a
* sda – stands for an SCSI Hard-disk a + SATA + USBsda1 for example, it would mean the first partition of the SCSI drive a.

>>> ??? 待确认
>>> 有多个同类型的控制器时，如多个 sata 控制器，顺序会乱

### 分区编号

十进制数字，编号从 1 开始

记住，最后两个字符，顺序和编号更重要：
* sda5 = 5th partition on the first HD (‘a’ is first HD)
* sdc8 = 8th partition on the third HD (‘c’ is third of three active HDs)
* sdb3 = 3rd partition on the second HD (‘b’ is second of two or more active HDs)
* hda1 = first partition of ide drive a.

## Grub

Grub 是一个程序，准确的说是引导操作系统启动的程序(bootloader)，存储在磁盘的引导
扇区，由 BIOS、UEFI 去引导扇区找到后，由 Grub 负责找到并启动操作系统。

Grub 对于磁盘的编号规则和 Linux 系统不同，其磁盘只有 fd 和 hd，磁盘编号从 0 开始：
* hd0       Harddrive (not depending on the Hardware) id 0.
* hd0-1     Harddive 0 partition 1.
* (hd0,5) = sda5
* (hd2,8) = sdc8
* (hd1,3) = sdb3

注意 Grub 程序也在更新，分为经典 Grub 和 Grub2，上面的语法是 Grub2 的语法，对经典
Grub 来说语法是错的。

更多参考：
[The map between BIOS drives and OS devices](http://www.gnu.org/software/grub/manual/grub/html_node/Device-map.html)
[Grub 的设备语法](http://www.gnu.org/software/grub/manual/grub/html_node/Device-syntax.html#Device-syntax)

