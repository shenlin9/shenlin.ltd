---
title: linux - iptables
categories:
  - Linux
  - iptables
tags:
  - Linux
  - iptables
---

iptables

<!--more-->

在刚买的ceno 7服务器中安装vsftpd之后想打开防火墙端口  结果/etc/sysconfig/目录下没有iptables文件  这时候就需要自己写一个iptables文件并且写入相关指令  然后使用 service iptables save 时显示 The service command supports only basic LSB actions (start, stop, restart, try-restart, reload, force-reload, status). For other actions, please try to use systemctl.

错误
```bash
[shenlin@t460p ~]$ sudo service iptables save
The service command supports only basic LSB actions (start, stop, restart, try-restart, reload, force-reload, status). For other actions, please try to use systemctl.
```

解决
```bash
[shenlin@t460p ~]$ sudo systemctl stop firewalld

[shenlin@t460p ~]$ sudo yum install -y iptables-services
Installed:
  iptables-services.x86_64 0:1.4.21-18.2.el7_4                                                                                                                                     
Dependency Updated:
  iptables.x86_64 0:1.4.21-18.2.el7_4                                                                                                                                              
Complete!

[shenlin@t460p ~]$ sudo systemctl enable iptables
Created symlink from /etc/systemd/system/basic.target.wants/iptables.service to /usr/lib/systemd/system/iptables.service.

[shenlin@t460p ~]$ sudo systemctl start iptables

```
首先不管防火墙有没有关 都使用systemctl stop firewalld 关闭防火墙
然后使用 yum install iptables-services 安装或更新服务
再使用systemctl enable iptables 启动iptables
最后 systemctl start iptables 打开iptables
大功告成
试试service iptables save



防火墙策略都是写在/etc/sysconfig/iptables文件里面的，如果没有此文件，解决：
1. 随便写一条iptables命令配置个防火墙规则。如：iptables -P OUTPUT ACCEPT。
2. service iptables save进行保存
3. service iptables restart命令重启
