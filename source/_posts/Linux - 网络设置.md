---
title: Linux - 网络设置
categories:
  - Linux
tags:
  - Linux
---

Linux 网络设置

<!--more-->

CentOS 7
```
[shenlin@t460p ~]$ sudo vi /etc/sysconfig/network-scripts/ifcfg-enp0s3

    TYPE=Ethernet
    PROXY_METHOD=none
    BROWSER_ONLY=no
    DEFROUTE=yes
    IPV4_FAILURE_FATAL=no
    IPV6INIT=yes
    IPV6_AUTOCONF=yes
    IPV6_DEFROUTE=yes
    IPV6_FAILURE_FATAL=no
    IPV6_ADDR_GEN_MODE=stable-privacy
    UUID=8969c66d-09f0-43aa-ae48-bd6d7b1e7f27

    NAME=enp0s3
    DEVICE=enp0s3
    BOOTPROTO=static        # 静态地址，还可为 dhcp
    IPADDR=10.63.165.152    
    NETMASK=255.255.240.0
    GATEWAY=10.63.160.1
    DNS1=10.61.3.1
    DNS2=172.30.204.111
    ONBOOT=yes              # 开机启用本配置，一般在最后一行

[shenlin@t460p ~]$ sudo systemctl restart network.service
```
