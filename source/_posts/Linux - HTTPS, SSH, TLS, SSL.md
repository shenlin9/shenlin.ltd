---
title: Linux - HTTPS, SSH, TLS, SSL
categories:
  - Linux
tags:
  - Linux
  - HTTPS
  - SSH
  - TLS
  - SSL
---

查询是否安装ssh
```
rpm -qa | grep ssh
```

重启SSH服务
```
service sshd restart
```

查看是否启动22端口
```
netstat -antp | grep sshd
```

设置ssh服务开机启动
```
chkconfig sshd on 
```

HTTPS=HTTP+SSL/TLS

SSL指的是建立一条加密链路以加密邮件通讯，其实这个链路中传输的内容是没有被SSL加密的。
