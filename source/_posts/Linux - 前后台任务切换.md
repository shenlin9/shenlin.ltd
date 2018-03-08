---
title: Linux - 前后台任务切换
categories:
  - Linux
tags:
  - jobs
  - fg
  - bg
---

http://www.tldp.org/guides.html

<!--more-->

jobs 查看正在运行的任务

     [ ]里的1，2,3代表任务号
     +表示最后一个放入后台的命令,也是fg不指定任务号时的默认恢复任务
     -表示倒数第二个放入后台的命令

```bash
$ jobs
[1]   Stopped                 vim 1.txt
[2]-  Stopped                 vim 2.txt
[3]+  Stopped                 vim 3.txt
```

`&` 把`&`号放命令后面，则命令将在后台运行
```bash
$ vim /etc/profile.d/custom.sh &
[1] 5924
```

Ctrl+z 暂停当前任务
```bash
[1]+ Stopped /root/bin/rsync.sh 
```

bg [任务号] : 把任务切换到后台运行
```bash
$ bg 1
[1]+ /root/bin/rsync.sh &
```

fg [任务号] : 把任务切换到前台运行
```bash
$ fg 1
/root/bin/rsync.sh
```


