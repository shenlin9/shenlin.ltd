---
title: Linux - SELinux
categories:
  - Linux
  - SELinux
tags:
  - Linux
  - SELinux
---

SELinux

<!--more-->

```bash
# 获取 selinux 状态
$ getenforce

# 改为 enforcing
$ setenforce 1

# 改为 permissive
$ setenforce 0

# 修改配置文件，改为 disable 永久关闭
# 需要重启 ???
$ sudo vi /etc/selinux/config

    # This file controls the state of SELinux on the system.  
    # SELINUX= can take one of these three values:  
    #       enforcing - SELinux security policy is enforced.  
    #       permissive - SELinux prints warnings instead of enforcing.  
    #       disabled - SELinux is fully disabled.  
    SELINUX=disabled  

    # SELINUXTYPE= type of policy in use. Possible values are:  
    #       targeted - Only targeted network daemons are protected.  
    #       strict - Full SELinux protection.  
    SELINUXTYPE=targeted
```
