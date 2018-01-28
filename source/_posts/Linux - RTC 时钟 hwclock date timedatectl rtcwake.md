---
title: Linux - RTC 时钟 hwclock date timedatectl rtcwake
categories:
  - Linux
tags:
  - Linux
  - RTC时钟
  - hwclock
  - date
  - timedatectl
  - rtcwake
---

系统用两个时钟保存时间：硬件时钟和系统时钟。

硬件时钟(即实时时钟 RTC 或 CMOS 时钟)

<!--more-->

## 什么是 RTC

RTC 是“RealTimeClock”的简称。

RTC时钟一般由板载电池供电，系统掉电后仍可以照常运行，系统启动的时候从RTC读取时间作为系统时间的初始值，系统启动以后内核会根据系统中断不停的更新系统时间，并每过11分钟将内核维护的系统时间写入RTC一次。系统掉电之后，RTC的时间不会丢失，而且会根据输入的震荡时钟信号不停的更新自己的时间。

除了内核，用户空间也可以通过设备节点访问RTC。用户空间可以读取或者设置RTC的时间值，也可以在RTC的设备节点上睡眠，并设置RTC何时将自己唤醒，这对于需要定时或者周期性的唤醒的用户进程特别有用。

硬件时钟(即实时时钟 RTC 或 CMOS 时钟)

    仅能保存年、月、日、时、分、秒这些时间数值，无法保存时间标准(UTC 或 localtime)和是否使用夏令时调节。大家还记得1秒的定义吗：1967年召开的国际计量大会上，一秒钟被定义为铯原子的9192631770次固有微波振荡次数所需的时间，这一标准沿用至今。而硬件时钟原理类似，也是通过石英晶体振荡器频率工作，详情可以去看实时时钟的百度百科。特性是系统关闭后，只要供电就会持续计时。

系统时钟(即软件时间)

    与硬件时间分别维护，保存了时间、时区和夏令时设置。Linux 内核保存为自 UTC 时间 1970 年1月1日 00:00:00 经过的秒数。初始系统时钟是从硬件时间计算得来，计算时会考虑/etc/adjtime的设置。由于这个文件的存在，即使硬件时钟设置的为UTC时间，系统也能显示正确的本地时间。系统启动之后，系统时钟与硬件时钟独立运行。

系统时间管理流程

    启动时使用RTC时间设置系统时间，并读取 /etc/adjtime 调整时间
    运行时通过系统中断不停的更新系统时间，并每个 11 分钟用系统时间更新RTC时间
    关机后RTC根据振荡时钟信号更新自己的时间

    通过 dmesg|grep rtc 和 sudo systemctl stop ntp 命令可以证实。

附注

    date命令仅更改系统时钟；
    timedatectl set-time 命令会同时影响硬件时钟和系统时钟 : http://www.codeceo.com/article/linux-timedatectl-set-time.html
    ArchWiki上有更详细的讲解 : https://wiki.archlinux.org/index.php/Time_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#RTC_clock

## 如何访问板载RTC？

### 通过hwclock软件

需要管理员（root）权限 常用命令：

sudo hwclock -r读取当前硬件时钟
sudo hwclock -s 以系统时钟为准写入硬件时钟
sudo hwclock -w 以硬件时钟为准设置系统时钟
sudo hwclock -u 设置硬件时钟为UTC标准

时间表示有两个标准：localtime 和 UTC 。
UTC(Coordinated Universal Time 协调世界时) 和 GMT (Greenwich Mean Time 格林威治时间) 都是与时区无关的全球时间标准。localtime 标准则依赖于当前时区。

这里建议将硬件时钟设置为UTC标准，以避免使用localtime时可能引起的麻烦和bug。前文已经讲过，系统读取硬件时钟后会根据/etc/adjtime调整（+8h）。

### 通过用户空间访问RTC

设备地址在/dev/rtcX X为编号

`cat /proc/driver/rtc` 可以看到RTC时钟信息，仅记录时间数值。

在/sys/class/rtc/rtcX目录下，可以访问控制RTC参数，详细：http://blog.chinaunix.net/uid-27041925-id-3672085.html

`ls /sys/class/rtc/rtcX`

## 如何使用板载RTC自动从低功耗状态唤醒

linux提供了一个工具rtcwake,可以设置系统延时、定时从低功耗状态唤醒。

因为RTC的特点是即使系统不运行，也能继续计时。而需要在系统运行时计时的应用就没必要用RTC了。

查看系统支持哪几种低功耗状态：
```bash
$ sudo cat /sys/power/state
```
freeze：Suspend-To-Idle，纯软件实现，冻结进程+挂起设备+使处理器空闲，Kernel 3.9引入
standby：Power-On Suspend，唤醒速度快，CPU在电
mem：Suspend-to-RAM，仅内存在电，其他设备处于低功耗状态
disk：Suspend-to-disk，也就是常说的休眠状态，将内存内容保存到磁盘。
详细：https://www.kernel.org/doc/Documentation/power/states.txt

我们可以直接使用参数使设备进入相应的状态，例如：
echo freeze > /sys/power/state 将会使系统进入freeze状态
如何唤醒请参考PMU手册，然而一般我都是直接插拔电源:-D

## 使用rtcwake唤醒设备

例1：延迟指定时间后唤醒
```bash
$ sudo rtcwake -m mem -s 20 -v
```
意思是进入mem状态并于20秒后唤醒，-v会获得更多信息

例2：在指定时间唤醒
```bash
$ sudo rtcwake -m mem -t `date -d 05:00 +%s`
```
意思是进入mem状态并于本地时间5:00唤醒（05:00和5:00效果一样，注意date命令的%s参数）。因为前文我们设置RTC使用UTC标准，而硬件时钟仅仅记录时间数值，所以系统通过-a参数来读取/etc/adjtime来调整时间，不过这是默认参数，可以不写。

上面讲了几种低功耗状态，此外rtcwake还支持以下参数：

    off：Poweroff，也就是关机
    no：Don't suspend，啥都不干，仅仅设置RTC Alarm时间
    on：Don't suspend，但是会读取RTC时间，等待RTC Alarm。主要用于调试。
    diable：禁用之前设置的RTC Alarm
    show：显示RTC Alarm信息

各种省电模式可通过dmesg命令查看具体发生了什么。

## 简单应用

此时我们就可以编写shell脚本，通过Cron计划任务让系统于指定时间添加rtcwake任务后自动关机。

拓展阅读
freeze state：http://kernelnewbies.org/Linux_3.9#head-1e61261c0d231fce38d9c738540ac01e59648d80
Automatically sleep and wake-up at specific times：http://askubuntu.com/questions/61708/automatically-sleep-and-wake-up-at-specific-times
Linux 下利用rtcwake唤醒设备：http://blog.csdn.net/bulreed/article/details/19907691

