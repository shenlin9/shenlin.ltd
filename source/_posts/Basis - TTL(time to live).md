---
title: Basis - TTL(time to live)
categories:
- Basis
tags:
- Basis
- TTL
date: 2019-10-01 14:09:54
---

Basis - TTL(time to live)

<!--more-->

## TTL

TTL, time to live，是一种数据包的失效机制，用来避免数据包被路由器无限制的转发下
去。

最初的时候，TTL 确实如其名字的含义一样，是表示允许数据包在网络上存活的最大秒数，
超过此时间，路由器即可丢弃此数据包。

现在的 TTL 其含义已经改变，表示允许跳过的路由器数，每经过一个路由器时，路由器就
把数据包的 TTL 值减 1，然后传递给下个路由器，减到 0 时则被路由器抛弃。

路由器丢弃数据包后，会向原数据包发送方反馈一个 ICMP 数据包。

IPv6 已经不使用 TTL，而改为 Hop Limit。

## TTL 的应用

### Ping

ping 和 tracert/traceroute 都使用到了 TTL。

当客户端去 Ping 一个服务器时，TTL 的最初值是服务器设置的，然后每经过一个路由器减
去 1，直到到达客户端。

即 windows 主机去 ping linux 主机时，ping 里的 TTL 由 linux 主机设置，反之，当
linux 主机 ping windows 主机时，ping 里的 TTL 由 windows 主机设置。

不同的系统，其默认的 TTL 值不一样，所以可以根据 Ping 返回的 TTL 值判断服务器的操
作系统类型，如 TTL 为 55，因其更接近 64，所以服务器是 *nix 或 win10：

    *nix (Linux/Unix)	64
    Win10               64
    Windows	            128
    Solaris/AIX	        254
    cisco device        255

测试本机的默认 TTL，使用：
```
ping -4 localhost
```

### Traceroute

用于 traceroute 命令时，一连串的数据包从被发送方向目的地传递，每经过一个路由器，
就把这些数据包的 TTL 加 1，然后抛弃其中一个数据包，并返回一个 ICMP 数据包给发送
方，返回数据包到发送方的时间即是达到此路由器的时间。

### CDN

CDN 通常使用 TTL 来确定从 CDN 源服务器获取新副本之前，CDN 边缘服务器缓存的内容可
以被提供多长时间。通过适当地设置源服务器拉取之间的时间量，CDN 能够提供更新的内容
，而不需要不断地向源服务器发送请求。这种优化允许 CDN 有效地向更接近用户的内容提
供服务，同时减少来自源的带宽需求。

### DNS

在DNS记录的上下文中，TTL 的数值决定了 DNS 缓存服务器在连接到权威DNS服务器并获得
该记录的新副本之前，可以为该DNS记录提供多长时间的服务。
