---
title: Linux - 熵池和随机数 - entropy, random, rng-tools
categories:
  - Linux
tags:
  - Linux
  - 熵池
  - entropy
---

Linux 的随机数

<!--more-->

Linux内核实现了一个随机数产生器，从理论上说这个随机数产生器产生的是真随机数。与标准C库中的rand(),srand()产生的伪随机数不同，尽管伪随机数带有一定的随机特征，但这些数字序列并非统计意义上的随机数。也就是说它们是可重现的--只要每次使用相同的seed值，就能得到相同的伪随机数列。通常通过使用time()的返回值来改变seed，以此得到不同的伪随机数序列，但time()返回值的结果并不是不确定的（可预测），也就是这里仍然缺少一个不确定的噪声源。对于需要真随机数的程序，都不能允许使用伪随机数。

 

为了获得真正意义上的随机数，需要一个外部的噪声源。Linux内核找到了一个完美的噪声源产生者--就是使用计算机的人。我们在使用计算机时敲击键盘的时间间隔，移动鼠标的距离与间隔，特定中断的时间间隔等等，这些对于计算机来讲都是属于非确定的和不可预测的。虽然计算机本身的行为完全由编程所控制，但人对外设硬件的操作具有很大的不确定性，而这些不确定性可以通过驱动程序中注册的中断处理例程(ISR)获取。

内核根据这些非确定性的设备事件维护着一个熵池，池中的数据是完全随机的。当有新的设备事件到来，内核会估计新加入的数据的随机性，当我们从熵池中取出数据时，内核会减少熵的估计值 ，如果熵估计值等于0了，内核此时可以拒绝用户对随机数的请求操作。


随机数在许多领域都有重要应用,如Monte Carlo模拟、密码学和网络安全。

随机数的质量直接关系到网络安全系统的可靠性和安全性，关系到 Monte Carlo模拟结果的可信度。

自从计算机诞生起，寻求用计算机产生高质量的随机数序列的研究就一直是个长期受到关注的课题。

Linux内核从 1.3.30版本开始实现了一个高强度的随机数发生器，本文根据Linux 2.6.10内核的源代码，详细分析该随机数产生器的设计与实现。

计算机本身是可预测的系统，因此，用计算机算法不可能产生真正的随机数。

但是机器的环境中充满了各种各样的噪声，如硬件设备发生中断的时间，用户点击鼠标的时间间隔等是完全随机的，事先无法预测。

Linux内核实现的随机数产生器正是利用系统中的这些随机噪声来产生高质量随机数序列。



熵（entropy）是描述系统混乱无序程度的物理量，一个系统的熵越大则说明该系统的有序性越差，即不确定性越大。

在信息学中，熵被用来表征一个符号或系统的不确定性，熵越大，表明系统所含有用信息量越少，不确定度越大。

Linux内核采用熵来描述数据的随机性。

Linux内核维护了一个熵池，用来收集来自设备驱动程序和其它来源的环境噪音，熵池是环境噪声数据流的集合，被作为种子用于生成随机数。

理论上，熵池中的数据是完全随机的，可以实现产生真随机数序列。为跟踪熵池中数据的随机性，内核在将数据加入池的时候将估算数据的随机性，这个过程称作熵估算。

熵估算值描述池中包含的随机数位数，其值越大表示池中数据的随机性越好。


熵池太浅会导致系统容易遭受已知攻击方法攻击，如容易被猜测出和容易遭受暴力破解攻击。

而且目前的服务器安全软件也很少去检查一个数据流是低熵还是高熵。

两个特殊的设备文件，都是字符型设备
区别在于：如果内核熵池的估计值为0时，random 将被阻塞，而 urandom 不会有这个限制
```bash
[shenlin@t460p ~]$ ll /dev/*random
crw-rw-rw-. 1 root root 1, 8 Jan 24 11:05 /dev/random   # 在不能产生新的随机数时会阻塞程序
crw-rw-rw-. 1 root root 1, 9 Jan 24 11:05 /dev/urandom  # 不会阻塞程序
```

/etc/sysconfig/rngd

可以看到 urandom 产生的随机数数量和速度都要胜过 random
```bash
[shenlin@t460p ~]$ dd if=/dev/random of=/dev/null bs=1024b count=1
0+1 records in
0+1 records out
115 bytes (115 B) copied, 0.000627454 s, 183 kB/s

[shenlin@t460p ~]$ dd if=/dev/urandom of=/dev/null bs=1024b count=1
1+0 records in
1+0 records out
524288 bytes (524 kB) copied, 0.00817059 s, 64.2 MB/s
```

可以用dd命令从/dev/urandom中获取指定字节数的随机值并写入文件中保存
```bash
[shenlin@t460p ~]$ dd if=/dev/urandom of=file2 count=1 bs=1024
1+0 records in
1+0 records out
1024 bytes (1.0 kB) copied, 0.000531632 s, 1.9 MB/s
```

查看系统当前熵池的大小
```bash
[shenlin@t460p ~]$ cat /proc/sys/kernel/random/entropy_avail 
3128
```

安装 rng-tools
```bash
[shenlin@t460p ~]$ sudo yum install -y rng-tools
```

修改 rng-tools 配置文件增加下列内容 ???没找到配置文件
```bash
[shenlin@t460p ~]$ sudo vi /etc/sysconfig/rngd
EXTRAOPTIONS="-r /dev/random"
```

启动 rngd 服务
```bash
[shenlin@t460p ~]$ systemctl start rngd
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to manage system services or units.
Authenticating as: shenlin
Password: 
==== AUTHENTICATION COMPLETE ===
```

或者一次性执行从随机数文件 urandom 输入随机数，默认是 `/dev/hwrng` 文件
```
[shenlin@t460p ~]$ sudo rngd -r /dev/urandom
```
