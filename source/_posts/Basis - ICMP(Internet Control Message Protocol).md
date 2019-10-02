---
title: Basis - ICMP(Internet Control Message Protocol)
categories:
- Basis
tags:
- Basis
- ICMP
date: 2019-10-01 16:28:33
---

Basis - ICMP(Internet Control Message Protocol)

<!--more-->

ICMP 是网络设备用来诊断网络通信问题的Internet层协议。ICMP主要用于确定数据是否及
时到达预定目的地。通常，ICMP协议用于网络设备，如路由器。

## 用处

**ICMP的主要目的是报告错误**

当两个设备通过Internet连接时，ICMP将生成错误，以便在任何数据没有到达预定目的地的
情况下与发送设备共享。

**ICMP协议的第二个用途是执行网络诊断**

常用的终端实用程序traceroute和ping都使用ICMP。

traceroute实用程序用于显示两个Internet设备之间的路由路径。
路由路径是连接路由器的实际物理路径，请求必须经过它才能到达目的地。
一个路由器与另一个路由器之间的旅程称为“单跳 hop”，traceroute还报告了路上每一次
单跳所需的时间。
这对于确定网络延迟的来源很有用。

ping实用程序是traceroute的简化版本。
ping将测试两台设备之间的连接速度，并准确地报告数据包到达目的地并返回发送方设备所需的时间。
虽然ping不提供关于路由或跃点的数据，但它仍然是度量两个设备之间延迟的非常有用的指标。
ICMP回送请求和回送应答消息通常用于执行ping。
不幸的是，网络攻击可以利用这一过程，造成中断的手段，如ICMP洪水攻击和Ping的死亡攻击。
