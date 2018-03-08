---
title: bash - linux shell 脚本攻略 - chapter 7 - 网络设置
categories: 
 - Linux
 - Bash
tags: 
 - 
---

shell 网络设置

<!--more-->

host 和 nslookup 经查询在安装包 bind-utils 里
```bash
-> yum provides "*/bin/host"
32:bind-utils-9.8.2-0.62.rc1.el6.i686 : Utilities for querying DNS name servers
Repo        : base
匹配来自于:
Filename    : /usr/bin/host

-> yum provides "*/bin/nslookup"
32:bind-utils-9.8.2-0.62.rc1.el6.i686 : Utilities for querying DNS name servers
Repo        : base
匹配来自于:
Filename    : /usr/bin/nslookup
```

安装
```bash
-> sudo yum install -y bind-utils
```

