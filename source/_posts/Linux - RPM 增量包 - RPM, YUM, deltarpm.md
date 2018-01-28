---
title: Linux - RPM 增量包 - RPM, YUM, deltarpm
categories:
  - Linux
tags:
  - Linux
  - RPM
  - YUM
  - deltarpm
---

deltarpm 软件包和 RPM 增量包

<!--more-->

yum update 时提示
```
[shenlin@t460p /usr/local/src]$ sudo yum update

Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
```

此命令属于 deltarpm 包
```
[shenlin@t460p /usr/local/src]$ yum provides '*/applydeltarpm'
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
deltarpm-3.6-3.el7.x86_64 : Create deltas between rpms
Repo        : base
Matched from:
Filename    : /usr/bin/applydeltarpm
```

deltarpm 是用于创建和应用增量 RPM 包的套件，还包括二进制文件：prepdeltarpm、writedeltarpm 和 applydeltarpm。

增量 RPM 包包含的是旧版本和新版本的 RPM 包之间的差别。

在旧 RPM 上应用增量 RPM 将得到全新的 RPM。

不需要旧 RPM 的副本，因为增量 RPM 可以与已安装的 RPM 一起工作。

增量 RPM 包的大小甚至比增补程序 RPM 小，这有利于通过因特网传送更新包。

增量 RPM 包缺点是，涉及增量 RPM 的更新操作与使用纯粹 RPM 或增补程序 RPM 进行更新的情况相比，占用的 CPU 周期要长得多。

更多参考：https://www.suse.com/zh-cn/documentation/sles10/book_sle_reference/data/sec.rpm.delta.html
