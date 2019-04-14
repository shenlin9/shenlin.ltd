---
title: Windows - 解决 Win10 服务主机本地系统网络受限
categories:
  - Windows
tags:
  - Windows
---

解决 Win10 服务主机本地系统网络受限

转载自：http://www.cnblogs.com/liweis/p/5179420.html

<!--more-->

## 现象

在进程中，服务主机：本地系统（网络受限）的CPU使用率非常高，经常导致达到100%使电脑卡起，甚是恼火。

## 解决方法

禁用显示名称为 Connected User Experiences and Telemetry 的服务即可。

## 原因

Windows 10有一个名为Diagnostics Tracking Service的服务，简称为DiagTrack，用于诊
断跟踪。说明了，就是键盘记录器，收集用户信息，不管它收集没有，都有点可耻了；但是
在升级TH2之后，这个服务竟然不见了，不是微软良心发现，而是用了障眼法，将显示名称
改为了Connected User Experiences and Telemetry，而实质没有改变。

