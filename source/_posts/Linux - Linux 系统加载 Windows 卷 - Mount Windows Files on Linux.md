---
title: Linux - Linux 系统加载 Windows 卷 - Mount Windows Files on Linux
categories:
- Linux
tags:
- Linux
- Mount Windows Files on Linux
date: 2020-02-23 09:20:03
---

Linux - Linux 系统加载 Windows 卷 - Mount Windows Files on Linux

<!--more-->

## 错误提示

Linux 启动时提示 "Unable to access the windows volume"

## 原因

Windows 未正常关闭，而是处于休眠状态

Windows 休眠时把当前的系统状态保存在硬盘的一个文件上，文件名 `hiberfil.sys`，

Windows 还在此文件中设置了一个标记，告诉其他操作系统 Windows 是休眠了而非关闭了。

Windows 休眠时，其他系统更改其分区内容则容易导致问题，如 Windows 恢复失败等，

所以工具 `ntfs-3g` 看到休眠标记时则不会继续挂载，准确的说，拒绝以读写模式挂载

Windows 卷。

## 解决方法

1. 进入 Windows 系统正常关闭退出

2. 以只读模式手动挂载 Windows 卷
```
sudo mkdir /media/windows
mount -t ntfs-3g -o ro /dev/sda3 /media/windows
```

3. 使用 ntfsfix
```
sudo ntfsfix /dev/sda2
```
sda2 表示要挂载的 Widows 分区

ntfsfix 作用：
* 修复一些基本的NTFS不一致性
* 重新设置NTFS日志文件
* 安排重新进入Windows时指向NTFS一致性检查


