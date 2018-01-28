---
title: Linux - 关机 shutdown poweroff 重启 reboot 挂起 suspend 定时唤醒 rtcwake 休眠 hibernate
categories:
  - Linux
tags:
  - Linux
---

halt
		vi. 停止；立定；踌躇，犹豫
		n. 停止；立定；休息
		vt. 使停止；使立定

halt on
		出错暂停

system halt
		系统中止；系统停止；系统挂起；死机

<!--more-->

体眠是一种更加省电的模式，它将内存中的数据保存于硬盘中，所有设备都停止工作。当再次使用时需按开关机键，机器将会恢复到您的执行休眠时的状态，而不用再次执行启动操作系统复杂的过程。

待机（挂起）是将当前处于运行状态的数据保存在内存中，机器只对内存供电，而硬盘、屏幕和CPU等部件则停止供电。由于数据存储在速度快的内存中，因此进入等待状态和唤醒的速度比较快。不过这些数据是保存在内存中，如果断电则会使数据丢失。

立刻关机：
```bash
sudo halt
sudo init 0
sudo shutdown -h now
sudo shutdown -h 0
```

定时/延时关机：
```bash
sudo shutdown -h 19:30
sudo shutdown -h +30      ##单位为分钟
```

重启：
```bash
sudo reboot
sudo init 6
sudo shutdown -r now
```

休眠：
```bash
sudo pm-hibernate
echo “disk” > /sys/power/state
sudo hibernate-disk
```

待机(挂起)：
```bash
sudo pm-suspend
sudo pm-suspend-hybrid

echo “mem” > /sys/power/state

sudo hibernate-ram
```

定时唤醒
```bash
$ rtcwake -m mem -s 60
```
表示系统挂起的时候是把当前系统的状态信息等保存到内存中，挂起时间为60秒，即在60秒后会自动唤醒；

RHEL 7 中，shutdown, poweroff, reboot, init 都是 systemctl 的链接。

-------------

系统挂起（Suspend）是电源管理（APM & ACPI）的一个特性，给用户带来了很大的方便。Linux在2.6系列核心中对电源管理有了较好的支持，下面就谈谈Linux对系统挂起的支持情况。

Linux对系统挂起的支持

    Linux同时提供了对APM和ACPI的支持，当时两者是不兼容的，同一时刻只能有一种机制工作。由于ACPI的优越性，所以现在Linux将ACPI设为缺省的电源管理方案。对于一些比较旧的主板，如果其BIOS中ACPI的实现在2000年以前，那么Linux自动启用APM（可以通过核心命令行参数acpi=force来强制启用ACPI）。如果你下主板BIOS中对ACPI的支持有些问题导致Linux工作不正常，那么还可以使用核心命令行参数acpi=off来强制禁用ACPI，这样Linux会自动启用APM电源管理。

Linux现在主要支持三种ACPI的节电方式：

    S1：Stopgrant，即待机（standby）模式。显示屏自动断电，只是主机通电。这时敲任意键即可恢复原来状态。

    S2 S3：STR（Suspend To Ram），即挂起到内存。系统把当前信息储存在内存中，只有内存等几个关键部件通电，这时计算机处在高度节电状态。此时系统不能从键盘唤醒。手工唤醒的方法只能是按前面板上的电源按钮。唤醒后，计算机从内存中读取信息很快恢复到原来状态。

    S4：STD（Suspend To Disk），即挂起到硬盘，也即休眠。计算机自动关机，关机前将当前数据存储在硬盘上，用户下次按开关键开机时计算机将无须启动操作系统，直接从硬盘读取数据，恢复原来状态。

在Linux下查看核心支持ACPI情况的方法如下：

2.4核心下：
```bash
$ sudo cat /proc/acpi/sleep

S0 S1 S3 S4 S5
```

2.6核心下：
```bash
$ cat /sys/power/state

standby mem disk
```

上面的输出可知，我们系统中核心同时支持三种节电模式。

在/sys/power目录下还有一个文件：disk，文件的内容可以如下：

    shutdown: 将系统状态保存到磁盘，让BIOS关闭计算机；platform: 将系统状态保存到磁盘，让BIOS关闭计算机，并且点亮挂起指示灯；firmware: 让BIOS自己将系统状态保存，并且关闭计算机，需要BIOS自己有挂起磁盘。大部分工作都由BIOS完成，对操作系统是透明的；

进入这三种节电模式的方法如下：

    #echo standby > /sys/power/state ---->挂起（S1）#echo mem > /sys/power/state ---->挂起到内存（S3）#echo shutdown > /sys/power/disk; echo disk > /sys/power/state ---->挂起到磁盘（S4）#echo platform > /sys/power/disk; echo disk > /sys/power/state

Linux下的磁盘挂起（STD）是通过swsusp机制实现的：将系统当前状态保存的内存后，再把内存内容写入交换分区（swap）。这里要求交换分区容量最好大于内存容量。系统挂起到磁盘后，下次启动的时候需要向核心传递命令行参数resume=/dev/hdaX（/dev/hdaX是系统中的交换分区），这样系统就能够很快恢复到关机时的状态。

还有一个非正式的核心补丁可以实现STD：Software Suspend 2。该项目是一个快速发展的项目，设计上教swsusp有一些优势，但是还没有集成到核心正式发布中，实现方式与swsusp基本相同。

虽然Linux提供了系统挂起的机制，但是执行上面的挂起操作不一定能够成功。一方面，这些操作除了需要BIOS支持以外，还需要外围硬件设备能够兼容，即设备支持节电状态，支持从节电状态或断电状态恢复；另一方面，这些设备驱动必须能够接收电源管理指令。目前，系统挂起的主要障碍就是那些还不太完善的驱动程序，如USB、显卡、声卡驱动等。

当然，现在Linux核心对系统挂起的支持还有待改进，主要表现在：

    不支持SMP系统。
    不支持大内存（>4G）。
    核心中许多模块需要增加电源管理的支持。
    缺少上层配置程序。
    不过以放心，所有的问题内核黑客们都能够解决！

https://weibo.com/dajiakuishow?refer_flag=0000015010_&from=feed&loc=nickname

参考阅读：

    http://www.acpi.info：ACPI的官方网站，在上面可以免费获得最新的ACPI规范。
    http://acpi.sourceforge.net：Linux下支持ACPI项目网站。官方Linux内核中ACPI的版本实际上已经远远落后于最新的版本，因为linux稳定版中对任何新特性的加入都是非常小心谨慎的。你可以从这里下载最新的ACPI补丁。
    Linux核心源代码目录：Documentation/power/，里面有开发人员写的一些关于电源管理在Linux上实现的文档。
    http://www.suspend2.net：Software Suspend 2的官方网站，STD的另一个解决方案。
