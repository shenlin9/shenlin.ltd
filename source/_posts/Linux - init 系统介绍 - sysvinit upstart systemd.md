---
title: Linux - init 系统介绍 - sysvinit upstart systemd
categories:
  - Linux
tags:
  - Linux
  - sysvinit
  - upstart
  - systemd
  - runlevel
  - target
---

转载自： [浅析 Linux 初始化 init 系统 1](https://www.ibm.com/developerworks/cn/linux/1407_liuming_init1/index.html?ca=drs-)，[2](https://www.ibm.com/developerworks/cn/linux/1407_liuming_init2/index.html?ca=drs-)，[3](https://www.ibm.com/developerworks/cn/linux/1407_liuming_init3/index.html?ca=drs-)

主要介绍三个 Init 系统：sysvinit，upstart 和 systemd。

<!--more-->

## 什么是 Init 系统

init 是 initialization 缩写

https://zh.wikipedia.org/zh-cn/Init

### init 进程

linux 启动时由 bootloader 载入内核并初始化，内核初始化的最后一步就是启动 pid 为 1 的 init 进程，

启动系统时，init 进程是系统产生的第一个进程。它负责产生其他所有用户进程。 

关闭系统时，init 是最后一个退出的进程。???confirm

init 以守护进程方式存在，是所有其他进程的祖先。

init 进程非常独特，能够完成其他进程无法完成的任务。

### Init 系统

Init 系统能够定义、管理和控制 init 进程的行为。

它负责组织和运行许多独立的或相关的始化工作(因此被称为 init 系统)，从而让计算机系统进入某种用户预订的运行模式。

Init 系统仅仅将内核运行起来是毫无实际用途的，必须由 将操作系统代入可操作状态。比如启动外壳 shell 后，便有了人机交互，这样就可以让计算机执行一些预订程序完成有实际意义的任务。或者启动 X 图形系统以便提供更佳的人机界面，更加高效的完成任务。这里，字符界面的 shell 或者 X 系统都是一种预设的运行模式。

### Init 系统的历史

在 Linux 主要应用于服务器和 PC 机的时代，SysVinit 运行非常良好，概念简单清晰。它主要依赖于 Shell 脚本，这就决定了它的最大弱点：启动太慢。在很少重新启动的 Server 上，这个缺点并不重要。

而当 Linux 被应用到移动终端设备的时候，启动慢就成了一个大问题。为了更快地启动，人们开始改进 sysvinit，先后出现了 upstart 和 systemd 这两个主要的新一代 init 系统。Upstart 已经开发了 8 年多，在不少系统中已经替换 sysvinit。Systemd 出现较晚，但发展更快，大有取代 upstart 的趋势。

### Init 系统的现状

不同的 Linux 发行版采用了不同的 init 实现：

大多数 Linux 发行版的 init 系统是和 System V 相兼容的，被称为 sysvinit。这是人们最熟悉的 init 系统。

RHEL/CentOS 从版本 6 开始采用 upstart 替代了传统的 sysvinit。

RHEL5 是 sysinit ，RHEL6 是 upstart，RHEL7 是 systemd   ????确认

Fedora 从版本 15 开始使用 systemd

Gentoo 是自己定制的。

一些发行版如 Slackware 采用的是 BSD 风格 Init 系统，这种风格使用较少，本文不再涉及。

## sysvinit

sysvinit 就是 System V 风格的 init 系统。

> Unix 根据操作风格分为 System V 和 BSD 两种，代表分别是 Solaris 和 FreeBSD，而 Linux不能称为"标准的Unix“而只被称为"Unix Like"原因有一部分就是因为它的操作风格介乎两者之间，而且不同的厂商为了照顾不同的用户，各linux发行版本的操作风格之间也有不小的出入。

> 所谓的操作风格不同，指的是例如： Root 脚本位置 System V 是位于 /etc/init.d 而 BSD 则位于 /etc/rc.d ，字符串函数 System V 是 memcopy 而 BSD 则是 bcopy ...

Sysvinit 不仅需要负责初始化系统，还需要负责关闭系统。 ??? upstart systemd 是否也负责关闭系统

### runlevel

sysvinit 在文件 '/etc/inittab' 中定义了运行级别 runlevel。

sysvinit 使用 runlevel 运行级别来定义系统的运行模式，不同的运行模式下系统需要初始化运行的进程和需要进行的初始化准备都是不同的。

通常会有 8 种运行模式，即运行模式 0 到 6 和 S 或者 s。 每种 Linux 发行版对运行模式的定义都不太一样。但 0，1，6 却得到了大家的一致赞同：

    0 关机
    1 单用户模式
    6 重启

模式 1, S 等往往用于系统故障之后的排错和恢复

'initdefault' 项定义了系统的默认运行级别。如果没有默认的运行模式，那么用户将进入系统控制台，手动决定进入何种运行模式。

### 初始化系统顺序

首先，sysvinit 读取，分析 /etc/inittab 文件获取配置信息，包括：

    系统需要进入的 runlevel
    捕获组合键的定义
    定义电源 fail/restore 脚本
    启动 getty 和虚拟控制台

然后，sysvinit 顺序地执行以下步骤：

    /etc/rc.d/rc.sysinit        ???这个文件不是 rc.sysvinit
    /etc/rc.d/rc
    /etc/rc.d/rcX.d/S*
    /etc/rc.d/rc.local

    详细说明：

    1. 执行 /etc/rc.d/rc.sysinit 进行一些重要的系统初始化任务，RHEL5 的任务包括：

        激活 udev 和 selinux
        设置定义在 /etc/sysctl.conf 中的内核参数
        设置系统时钟
        加载 keymaps
        使能交换分区
        设置主机名(hostname)
        根分区检查和 remount
        激活 RAID 和 LVM 设备
        开启磁盘配额
        检查并挂载所有文件系统
        清除过期的 locks 和 PID 文件

    2. 运行 /etc/rc.d/rc 和 /etc/rc.d/rcX.d/ 脚本

        开始运行/etc/rc.d/rc 脚本，rc 脚本将打开对应该 runlevel 的 rcX.d 目录，找到并运行存放在该目录下的所有启动脚本。

            X 代表运行级别 0-6，每个 runlevel X 都有一个这样的目录，目录名为/etc/rc.d/rcX.d。

            这些目录下存放着很多不同的脚本。文件名以 S 开头的脚本就是启动时应该运行的脚本，S 后面跟的数字定义了这些脚本的执行顺序。
            
            在/etc/rc.d/rcX.d 目录下的脚本都是一些软链接文件，真实的脚本文件存放在/etc/init.d 目录下。

    3. 运行 /etc/rc.d/rc.local 脚本

        rc.local 是用户进行个性化设置的地方，因为一台 Linux Server 的用户一般不止一个。

    4. X Display Manager

        如果需要的话

执行完毕后，就将系统初始化为了用户设定的 runlevel。可见 Sysvinit 是巧妙地用脚本，文件命名规则和软链接来实现的不同 runlevel。

### 关闭系统顺序

顺序的执行 /etc/rc.d/rcX.d/ 目录下的以 K 开头的脚本文件

    在系统关闭时，为了保证数据的一致性，需要小心地按顺序进行结束和清理工作。

    比如应该先停止对文件系统有读写操作的服务，然后再 umount 文件系统。否则数据就会丢失。

    这种顺序的控制这也是依靠/etc/rc.d/rcX.d/目录下所有脚本的命名规则来控制的，在该目录下所有以 K 开头的脚本都将在关闭系统时调用，字母 K 之后的数字定义了它们的执行顺序。

    这些脚本负责安全地停止服务或者其他的关闭工作。

### 管理和控制功能

原始的 sysvinit 软件包包含了一系列的控制启动，运行和关闭所有其他程序的工具。

    halt
    停止系统。

    init
    这个就是 sysvinit 本身的 init 进程实体，以 pid1 身份运行，是所有用户进程的父进程。最主要的作用是在启动过程中使用/etc/inittab 文件创建进程。

    killall5
    向除自己的会话(session)进程之外的其它进程发出信号，所以不能杀死当前使用的 shell。（就是 SystemV 的 killall 命令。）

    last
    回溯/var/log/wtmp 文件(或者-f 选项指定的文件)，显示自从这个文件建立以来，所有用户的登录情况。

    lastb
    作用和 last 差不多，默认情况下使用/var/log/btmp 文件，显示所有失败登录企图。

    mesg
    控制其它用户对用户终端的访问。

    pidof
    找出程序的进程识别号(pid)，输出到标准输出设备。

    poweroff
    等于 shutdown -h -p，或者 telinit 0。关闭系统并切断电源。

    reboot
    等于 shutdown -r 或者 telinit 6。重启系统。

    runlevel
    读取系统的登录记录文件(一般是/var/run/utmp)把以前和当前的系统运行级输出到标准输出设备。

    shutdown
    以一种安全的方式终止系统，所有正在登录的用户都会收到系统将要终止通知，并且不准新的登录。

    sulogin
    当系统进入单用户模式时，被 init 调用。当接收到启动加载程序传递的-b 选项时，init 也会调用 sulogin。

    telinit
    实际是 init 的一个连接，用来向 init 传送单字符参数和信号。

    utmpdump
    以一种用户友好的格式向标准输出设备显示/var/run/utmp 文件的内容。

    wall
    向所有有信息权限的登录用户发送消息。

不同的 Linux 发行版在这些 sysvinit 的基本工具基础上又开发了一些辅助工具用来简化 init 系统的管理工作。

比如 RedHat 的 RHEL 在 sysvinit 的基础上开发了 initscripts 软件包，包含了大量的启动脚本 (如 rc.sysinit) ，还提供了 service，chkconfig 等命令行工具，甚至一套图形化界面来管理 init 系统。

只要理解了 sysvinit 的机制，在一个最简的仅有 sysvinit 的系统下，也可以直接调用脚本启动和停止服务，手动创建 inittab 和创建软连接来完成这些任务。因此理解 sysvinit 的基本原理和命令是最重要的。甚至也可以开发自己的一套管理工具。

### 命令

```bash
$ sudo /etc/init.d/apache2 start
# 或者
$ service apache2 start
```

### 优点

概念简单:

    Service 开发人员只需要编写启动和停止脚本，概念非常清楚；
    将 service 添加/删除到某个 runlevel 时，只需要执行一些创建/删除软连接文件的基本操作；
    这些都不需要学习额外的知识或特殊的定义语法(UpStart 和 Systemd 都需要用户学习新的定义系统初始化行为的语言)。

确定的执行顺序：

    脚本严格按照启动数字的大小顺序执行，一个执行完毕再执行下一个，这非常有益于错误排查。
    而UpStart 和 systemd 支持并发启动，导致没有人可以确定地了解具体的启动顺序，排错不易。

### 缺点

启动太慢

    sysvinit 启动是线性、顺序的，即所有的初始化步骤都是串行执行的，导致 sysvinit 运行效率较慢。

    运行时是同步阻塞的，一个脚本运行的时候，后续脚本必须等待。
    
    在 Linux 大量应用于很少重新启动的服务器时代，系统启动时间也许还不那么重要；

    然而对于桌面系统和便携式设备，启动时间的长短对用户体验影响很大。

    此外云计算等新的 Server 端技术也往往需要单个设备可以更加快速地启动。

启动脚本复杂

    init 进程只是执行启动脚本，不管其他事情。
    
    脚本需要自己处理各种情况，这往往使得脚本变得很长。

浪费资源，无法动态设备加载

    linux 在内核更新到2.6以后，不仅被用于服务器，还用于桌面和嵌入式设备。
    桌面和嵌入式设备的特点是会经常重启，因此对启动速度有要求，
    而且会按使用需要对硬件热拔插，比如 U 盘平时并不连接电脑，使用时才插入 USB 插口。

    Linux 在 2.6 内核支持下，一旦新外设连接到系统，内核便可以自动实时地发现它们，并初始化这些设备，
    但 sysinit 并不能根据硬件的变化自动的启动或停止对应的服务，只能一次性把所有可能用到的服务都启动起来，
    造成很多的资源浪费，例如：
    1. 为了管理打印任务，即使打印机没有连接，系统也需要启动 CUPS(Common Unix Printing System) 等服务
    2. 在/etc/fstab 中，可以指定系统自动挂载一个网络共享盘，比如 NFS，或者 iSCSI 设备。
       按执行顺序 sysvinit 先分析 /etc/fstab 挂载文件系统，然后再启动网络，但没有网络 NFS iSCSI 又无法访问和挂载，
       为了解决上面的矛盾，Sysvinit 采用了一个不得已的补救方法 netdev 来解决这个问题，即在 /etc/fstab 发现 netdev 属性挂载点的时候，
       不尝试挂载它，在网络初始化之后，再使用一个专门的 netfs 服务来挂载所有这些网络盘。
       这样的解决方法给管理员带来不便。部分新手管理员甚至从来也没有听说过 netdev 选项，因此经常成为系统管理的一个陷阱。
    3. 如 backup manager 等其它常驻内存，后台不间断运行却很少真正被使用的服务
    4. 如 Network Manager，一天之内用户很少切换网络设备，所以大部分时间 Network Manager 服务仅仅是在浪费系统资源
    5. 某些服务很可能在很长一段时间内，甚至整个服务器运行期间都没有被使用过。比如 CUPS，打印服务在多数服务器上很少被真正使用到。您可能没有想到，在很多服务器上 SSHD 也是很少被真正访问到的。花费在启动这些服务上的时间是不必要的；同样，花费在这些服务上的系统资源也是一种浪费。

## upstart

针对 sysinit 的不足，Ubuntu 开发人员重新设计和开发了全新的 init 系统 : UpStart。

UpStart 基于事件机制，主要的概念是 job 和 event。

Upstart 就是由 event 触发 job 运行的一个系统，每一个程序的运行都由其依赖的 event 发生而触发的。

比如插入 U 盘后，linux 的设备管理器 udev 得到内核通知，发现该设备，这就是一个 event。

UpStart 在感知到该事件之后触发的相应任务就是 Job，比如处理 /etc/fstab 中存在的挂载点。

采用这种事件驱动的模式，upstart 完美地解决了即插即用设备带来的新问题，更好的适应桌面和便携设备，它可以：

    更快地启动系统
    当新硬件被发现时动态启动服务
    硬件被拔除时动态停止服务

### Job

Job 就是一个工作的单元，一个任务或者一个服务，用来完成一件工作，比如启动一个后台服务，或者运行一个配置命令。

每个 Job 都等待一个或多个事件，一旦事件发生，upstart 就触发该 job 完成相应的工作。

有三种类型的 Job：

    task job；
        代表在一定时间内会执行完毕的任务，比如删除一个文件；

    service job；
        代表后台服务进程，比如 apache httpd。
        这类进程一般不会退出，一旦开始运行就成为一个后台守护进程，由 init 进程管理，如果这类进程退出，由 init 进程重新启动，
        它们只能由 init 进程发送信号停止。它们的停止一般也是由于所依赖的停止事件而触发的，
        不过 upstart 也提供命令行工具，让管理人员手动停止某个服务； 

    abstract job；
        仅由 upstart 内部使用，仅对理解 upstart 内部机理有所帮助。 

还可以按初始化对象分为两种类型的 Job：

    sytem job:
        系统的初始化任务就叫做 system job，比如挂载文件系统的任务就是一个 system job；

    session job:
        Upstart 还可以为每个用户会话（session）的初始化服务。
        用户会话的初始化服务就叫做 session job。

### Job 生命周期

Upstart 为每个工作都维护一个生命周期，为了更精细地描述工作的变化，Upstart 中 Job 的状态有:

|状态名|含义|
|---|---|
|Waiting |初始状态|
|Starting |Job 即将开始|
|pre-start |执行 pre-start 段，即任务开始前应该完成的工作|
|Spawned |准备执行 script 或者 exec 段|
|post-start |执行 post-start 动作|
|Running |interim state set after post-start section processed denoting job is running (But it may have no associated PID!)|
|pre-stop |执行 pre-stop 段|
|Stopping |interim state set after pre-stop section processed|
|Killed |任务即将被停止|
|post-stop |执行 post-stop 段|

但只有下面四个状态会引起 init 进程发送相应的事件，表明该工作的相应变化：

    Starting
    Started
    Stopping
    Stopped

而其它的状态变化不会发出事件。

### job 配置文件

任何一个 job 都是由一个工作配置文件（Job Configuration File）定义的。存放在 `/etc/init` 目录，以 `.conf` 作为文件名后缀。
工作配置文件是一个文本文件，包含一个或者多个小节（stanza）。
每个小节是一个完整的定义模块，定义了工作的一个方面，比如 author 小节定义了工作的作者。
比较重要的小节：

**"expect"**

Upstart 除了负责系统的启动过程之外，和 SysVinit 一样，Upstart 还提供一系列的管理工具。当系统启动之后，管理员可能还需要进行维护和调整，比如启动或者停止某项系统服务。或者将系统切换到其它的工作状态，比如改变运行级别。本文后续将详细介绍 Upstart 的管理工具的使用。

为了启动，停止，重启和查询某个系统服务。Upstart 需要跟踪该服务所对应的进程。比如 httpd 服务的进程 PID 为 1000。当用户需要查询 httpd 服务是否正常运行时，Upstart 就可以利用 ps 命令查询进程 1000，假如它还在正常运行，则表明服务正常。当用户需要停止 httpd 服务时，Upstart 就使用 kill 命令终止该进程。为此，Upstart 必须跟踪服务进程的进程号。

部分服务进程为了将自己变成后台精灵进程(daemon)，会采用两次派生(fork)的技术，另外一些服务则不会这样做。假如一个服务派生了两次，那么 UpStart 必须采用第二个派生出来的进程号作为服务的 PID。但是，UpStart 本身无法判断服务进程是否会派生两次，为此在定义该服务的工作配置文件中必须写明 expect 小节，告诉 UpStart 进程是否会派生两次。

Expect 有两种，"expect fork"表示进程只会 fork 一次；"expect daemonize"表示进程会 fork 两次。

**"exec" 和 "script"**

一个 UpStart 工作可能是运行一条 shell 命令，或者运行一段脚本。

用"exec"关键字配置工作需要运行的命令；

用"script"关键字定义需要运行的脚本。

```
# mountall.conf
description “Mount filesystems on boot”
start on startup
stop on starting rcS
...
script
  . /etc/default/rcS后台不间断运行却很少真正被使用的服务
  [ -f /forcefsck ] && force_fsck=”--force-fsck”
  [ “$FSCKFIX”=”yes” ] && fsck_fix=”--fsck-fix”
    
  ...
   
  exec mountall –daemon $force_fsck $fsck_fix
end script
...
```

**"start on"和"stop on"**

"start on"定义了触发工作的所有事件。"start on"的语法很简单，如下所示：

```
start on EVENT [[KEY=]VALUE]... [and|or...]
```
* EVENT 表示事件的名字，可以在 start on 中指定多个事件，表示该工作的开始需要依赖多个事件发生。多个事件之间可以用 and 或者 or 组合，"表示全部都必须发生"或者"其中之一发生即可"等不同的依赖条件。
* 除了事件发生之外，工作的启动还可以依赖特定的条件，因此在 start on 的 EVENT 之后，可以用 KEY=VALUE 来表示额外的条件，一般是某个环境变量(KEY)和特定值(VALUE)进行比较。如果只有一个变量，或者变量的顺序已知，则 KEY 可以省略。

"stop on"和"start on"非常类似，只不过是定义工作在什么情况下需要停止。

举例：
```
#dbus.conf
description     “D-Bus system message bus”
 
start on local-filesystems
stop on deconfiguring-networking
…
```
* D-Bus 是一个系统消息服务，详细见 [使用 D-BUS 连接桌面应用程序](https://www.ibm.com/developerworks/cn/linux/l-dbus.html)
* 上面的配置文件表明当系统发出 local-filesystems 事件时启动 D-Bus;
* 当系统发出 deconfiguring-networking 事件时，停止 D-Bus 服务。

### Event

事件在 upstart 中以通知消息的形式具体存在。一旦某个事件发生了，Upstart 就向整个系统发送一个消息。
没有任何手段阻止事件消息被 upstart 的其它部分知晓，也就是说，事件一旦发生，整个 upstart 系统中所有工作和其它的事件都会得到通知。

Event 可以分为三类: signal，methods 或者 hooks。

    Signals
    Signal 事件是非阻塞的，异步的。发送一个信号之后控制权立即返回。

    Methods
    Methods 事件是阻塞的，同步的。

    Hooks
    Hooks 事件是阻塞的，同步的。它介于 Signals 和 Methods 之间，调用发出 Hooks 事件的进程必须等待事件完成才可以得到控制权，但不检查事件是否成功。

事件是个非常抽象的概念，举例：

    系统上电启动，init 进程会发送"start"事件
    根文件系统可写时，相应 job 会发送文件系统就绪的事件
    一个块设备被发现并初始化完成，发送相应的事件
    某个文件系统被挂载，发送相应的事件
    类似 atd 和 cron，可以在某个时间点，或者周期的时间点发送事件
    另外一个 job 开始或结束时，发送相应的事件
    一个磁盘文件被修改时，可以发出相应的事件
    一个网络设备被发现时，可以发出相应的事件
    缺省路由被添加或删除时，可以发出相应的事件

不同的 Linux 发行版对 upstart 有不同的定制和实现，实现和支持的事件也有所不同，可以用 `man 7 upstart-events` 来查看事件列表。

### 系统管理员需要了解的 Upstart 命令

在 Upstart 系统中，修改默认启动运行级别需要修改 `/etc/init/rc-sysinti.conf` 中的 `DEFAULT_RUNLEVEL` 这个参数。

UpStart 提供了一系列的命令来管理系统服务。比如系统服务的监控，启动，停止和配置。

核心是initctl，这是一个带子命令风格的命令行工具。

**service 命令和 initctl 命令对照表**

|Service 命令|UpStart initctl 命令|
|---|---|
|service start |initctl start|
|service stop |initctl stop|
|service restart |initctl restart|
|service reload |initctl reload|
||initctl emit <event> 从命令行发送一个事件|

UpStart 还提供了一些快捷命令来简化 initctl，实际上这些命令只是在内部调用相应的 initctl 命令。
比如 reload，restart，start，stop 等等。启动一个服务可以简单地调用 `start <job>` 和 `initctl start <job>`

随 UpStart 发布的小工具，用来帮助开发 UpStart 或者诊断 UpStart 的问题：init-checkconf 和 upstart-monitor

## systemd

其他参考：
http://0pointer.de/blog/projects/why.html   why systemd?
http://mtoou.info/hing-systemd/
http://fedoraproject.org/wiki/Systemd/zh-cn

Systemd 的很多概念来源于苹果 Mac OS 操作系统上的 launchd。

根据 Linux 惯例，字母d是守护进程（daemon）的缩写。

Systemd 这个名字的含义，就是它要守护整个系统。

### 特点

**兼容性**

    systemd 引入了新的配置方式，对应用程序的开发也有一些新的要求。

    但 Systemd 兼容 Sysvinit 以及 LSB initscripts，因此很多已存在的代码无需更改。

**更高的并发性**

    在 UpStart 中，有依赖关系的服务还是必须先后启动，所以局部还是串行执行。

    Systemd 提供了比 UpStart 更激进的并行启动能力，更进一步提高了并发性，即便对于那些 UpStart 认为存在相互依赖而必须串行的服务，也可以并发启动。

    结果就是：更快的启动速度。

**按需启动**

    Systemd 可以提供按需启动的能力，只有在某个服务被真正请求的时候才启动它。
    
    当该服务结束，systemd 可以关闭它，等待下次需要时再次启动它。

**更科学的管理进程生命周期**

    init 系统的一个重要职责就是负责跟踪和管理服务进程的生命周期。它不仅可以启动一个服务，也必须也能够停止服务。这看上去没有什么特别的，然而在真正用代码实现的时候，您或许会发现停止服务比一开始想的要困难。

    服务进程一般都会作为守护进程（daemon）在后台运行，为此服务程序有时候会派生(fork)两次。在 UpStart 中，需要在配置文件中正确地配置 expect 小节。这样 UpStart 通过对 fork 系统调用进行计数，从而获知真正的守护进程的 PID 号。

    例如进程 p1 派生一次生成进程 p1`，p1` 再次派生生成进程 p1``，如果 UpStart 找错了，将 p1`作为服务进程的 Pid，那么停止服务的时候，UpStart 会试图杀死 p1`进程，而真正的 p1`` 进程则继续执行。换句话说该服务就失去控制了。

    还有更加特殊的情况。比如，一个 CGI 程序会派生两次，从而脱离了和 Apache 的父子关系。当 Apache 进程被停止后，该 CGI 程序还在继续运行。而我们希望服务停止后，所有由它所启动的相关进程也被停止。

    为了处理这类问题，UpStart 通过 strace 来跟踪 fork、exit 等系统调用，但是这种方法很笨拙，且缺乏可扩展性。
    
    systemd 则利用了 Linux 内核的特性即 CGroup 来跟踪和管理进程的生命周期。当停止服务时，通过查询 CGroup，systemd 可以确保找到所有的相关进程，从而干净地停止服务。

    CGroup 已经出现了很久，它主要用来实现系统资源配额管理。CGroup 提供了类似文件系统的接口，使用方便。当进程创建子进程时，子进程会继承父进程的 CGroup。因此无论服务如何启动新的子进程，所有的这些相关进程都会属于同一个 CGroup，systemd 只需要简单地遍历指定的 CGroup 即可正确地找到所有的相关进程，将它们一一停止即可。

**自带文件系统自动挂载管理**

    传统的 Linux 系统中，用户可以用 /etc/fstab 文件来维护固定的文件系统挂载点。这些挂载点在系统启动过程中被自动挂载，一旦启动过程结束，这些挂载点就会确保存在。这些挂载点都是对系统运行至关重要的文件系统，比如 HOME 目录。

    和 sysvinit 一样，Systemd 管理这些挂载点，以便能够在系统启动时自动挂载它们。Systemd 还兼容/etc/fstab 文件，您可以继续使用该文件管理挂载点。

    有时候用户还需要动态挂载点，比如打算访问 DVD 内容时，才临时执行挂载以便访问其中的内容，而不访问光盘时该挂载点被取消(umount)，以便节约资源。传统地，人们依赖 autofs 服务来实现这种功能。

    Systemd 内建了自动挂载服务，无需另外安装 autofs 服务，可以直接使用 systemd 提供的自动挂载管理能力来实现 autofs 的功能。

**实现事务性依赖关系管理**

    系统启动过程是由很多的独立工作共同组成的，这些工作之间可能存在依赖关系，比如挂载一个 NFS 文件系统必须依赖网络能够正常工作。Systemd 虽然能够最大限度地并发执行很多有依赖关系的工作，但是类似"挂载 NFS"和"启动网络"这样的工作还是存在天生的先后依赖关系，无法并发执行。对于这些任务，systemd 维护一个"事务一致性"的概念，保证所有相关的服务都可以正常启动而不会出现互相依赖，以至于死锁的情况。

**能够对系统进行快照和恢复**

    systemd 支持按需启动，因此系统的运行状态是动态变化的，人们无法准确地知道系统当前运行了哪些服务。
    
    Systemd 快照提供了一种将当前系统运行状态保存并恢复的能力。

    比如系统当前正运行服务 A 和 B，可以用 systemd 命令行对当前系统运行状况创建快照。然后将进程 A 停止，或者做其他的任意的对系统的改变，比如启动新的进程 C。在这些改变之后，运行 systemd 的快照恢复命令，就可立即将系统恢复到快照时刻的状态，即只有服务 A，B 在运行。一个可能的应用场景是调试：比如服务器出现一些异常，为了调试用户将当前状态保存为快照，然后可以进行任意的操作，比如停止服务等等。等调试结束，恢复快照即可。

    这个快照功能目前在 systemd 中并不完善，似乎开发人员也没有特别关注它，因此有报告指出它还存在一些使用上的问题，使用时尚需慎重。

**自带日志服务**

https://linuxtoy.org/archives/systemd-journal.html

systemd 自带日志服务 journald，该日志服务的设计初衷是克服现有的 syslog 服务的缺点。比如：

    syslog 不安全，消息的内容无法验证。
        每一个本地进程都可以声称自己是 Apache PID 4711，而 syslog 也就相信并保存到磁盘上。

    数据没有严格的格式，非常随意。
        自动化的日志分析器需要分析人类语言字符串来识别消息。一方面此类分析困难低效；此外日志格式的变化会导致分析代码需要更新甚至重写。

Systemd Journal 用二进制格式保存所有日志信息，用户使用 `journalctl` 命令来查看日志信息。无需自己编写复杂脆弱的字符串分析处理程序。

Systemd Journal 的优点如下：

    简单性：代码少，依赖少，抽象开销最小。
    零维护：日志是除错和监控系统的核心功能，因此它自己不能再产生问题。举例说，自动管理磁盘空间，避免由于日志的不断产生而将磁盘空间耗尽。
    移植性：日志 文件应该在所有类型的 Linux 系统上可用，无论它使用的何种 CPU 或者字节序。
    性能：添加和浏览 日志 非常快。
    最小资源占用：日志 数据文件需要较小。
    统一化：各种不同的日志存储技术应该统一起来，将所有的可记录事件保存在同一个数据存储中。所以日志内容的全局上下文都会被保存并且可供日后查询。例如一条固件记录后通常会跟随一条内核记录，最终还会有一条用户态记录。重要的是当保存到硬盘上时这三者之间的关系不会丢失。Syslog 将不同的信息保存到不同的文件中，分析的时候很难确定哪些条目是相关的。
    扩展性：日志的适用范围很广，从嵌入式设备到超级计算机集群都可以满足需求。
    安全性：日志 文件是可以验证的，让无法检测的修改不再可能。

### Systemd 相关概念

Systemd 可以管理所有系统资源。不同的资源统称为 Unit（单位）。

Unit 一共分成12种。

        Service unit：系统服务
        Target unit：多个 Unit 构成的一个组
        Device Unit：硬件设备
        Mount Unit：文件系统的挂载点
        Automount Unit：自动挂载点
        Path Unit：文件或路径
        Scope Unit：不是由 Systemd 启动的外部进程
        Slice Unit：进程组
        Snapshot Unit：Systemd 快照，可以切回某个快照
        Socket Unit：进程间通信的 socket
        Swap Unit：swap 文件
        Timer Unit：定时器

systemctl list-units命令可以查看当前系统的所有 Unit 。


    # 列出正在运行的 Unit
    $ systemctl list-units

    # 列出所有Unit，包括没有找到配置文件的或者启动失败的
    $ systemctl list-units --all

    # 列出所有没有运行的 Unit
    $ systemctl list-units --all --state=inactive

    # 列出所有加载失败的 Unit
    $ systemctl list-units --failed

    # 列出所有正在运行的、类型为 service 的 Unit
    $ systemctl list-units --type=service

4.2 Unit 的状态

systemctl status命令用于查看系统状态和单个 Unit 的状态。


    # 显示系统状态
    $ systemctl status

    # 显示单个 Unit 的状态
    $ sysystemctl status bluetooth.service

    # 显示远程主机的某个 Unit 的状态
    $ systemctl -H root@rhel7.example.com status httpd.service

除了status命令，systemctl还提供了三个查询状态的简单方法，主要供脚本内部的判断语句使用。


    # 显示某个 Unit 是否正在运行
    $ systemctl is-active application.service

    # 显示某个 Unit 是否处于启动失败状态
    $ systemctl is-failed application.service

    # 显示某个 Unit 服务是否建立了启动链接
    $ systemctl is-enabled application.service

4.3 Unit 管理

对于用户来说，最常用的是下面这些命令，用于启动和停止 Unit（主要是 service）。


    # 立即启动一个服务
    $ sudo systemctl start apache.service

    # 立即停止一个服务
    $ sudo systemctl stop apache.service

    # 重启一个服务
    $ sudo systemctl restart apache.service

    # 杀死一个服务的所有子进程
    $ sudo systemctl kill apache.service

    # 重新加载一个服务的配置文件
    $ sudo systemctl reload apache.service

    # 重载所有修改过的配置文件
    $ sudo systemctl daemon-reload

    # 显示某个 Unit 的所有底层参数
    $ systemctl show httpd.service

    # 显示某个 Unit 的指定属性的值
    $ systemctl show -p CPUShares httpd.service

    # 设置某个 Unit 的指定属性
    $ sudo systemctl set-property httpd.service CPUShares=500

4.4 依赖关系

Unit 之间存在依赖关系：A 依赖于 B，就意味着 Systemd 在启动 A 的时候，同时会去启动 B。

systemctl list-dependencies命令列出一个 Unit 的所有依赖。


    $ systemctl list-dependencies nginx.service

上面命令的输出结果之中，有些依赖是 Target 类型（详见下文），默认不会展开显示。如果要展开 Target，就需要使用--all参数。


    $ systemctl list-dependencies --all nginx.service


#### Unit 单元

系统初始化需要做的事情非常多。

需要启动后台服务，比如启动 SSHD 服务；

需要做配置工作，比如挂载文件系统。

这个过程中的每一步都被 systemd 抽象为一个配置单元，即 unit。

可以认为一个服务是一个配置单元；一个挂载点是一个配置单元；一个交换分区的配置是一个配置单元；等等。

systemd 将配置单元归纳为以下 12 种不同的类型:

    Service Unit ：代表一个后台服务进程，比如 mysqld。这是最常用的一类。

    Socket Unit ：进程间通信的 socket，此类配置单元封装系统和互联网中的一个 套接字 。当下，systemd 支持流式、数据报和连续包的 AF_INET、AF_INET6、AF_UNIX socket 。每一个套接字配置单元都有一个相应的服务配置单元 。相应的服务在第一个"连接"进入套接字时就会启动(例如：nscd.socket 在有新连接后便启动 nscd.service)。

    Device Unit ：硬件设备，此类配置单元封装一个存在于 Linux 设备树中的设备。每一个使用 udev 规则标记的设备都将会在 systemd 中作为一个设备配置单元出现。

    Mount Unit ：文件系统的挂载点，此类配置单元封装文件系统结构层次中的一个挂载点。Systemd 将对这个挂载点进行监控和管理。比如可以在启动时自动将其挂载；可以在某些条件下自动卸载。Systemd 会将/etc/fstab 中的条目都转换为挂载点，并在开机时处理。

    Automount Unit ：自动挂载点，此类配置单元封装系统结构层次中的一个自挂载点。每一个自挂载配置单元对应一个挂载配置单元 ，当该自动挂载点被访问时，systemd 执行挂载点中定义的挂载行为。

    Swap Unit : swap 文件，和挂载配置单元类似，交换配置单元用来管理交换分区。用户可以用交换配置单元来定义系统中的交换分区，可以让这些交换分区在启动时被激活。

    Target Unit ：多个 Unit 构成的一个组，此类配置单元为其他配置单元进行逻辑分组。它们本身实际上并不做什么，只是引用其他配置单元而已。这样便可以对配置单元做一个统一的控制。这样就可以实现大家都已经非常熟悉的运行级别概念。比如想让系统进入图形化模式，需要运行许多服务和配置命令，这些操作都由一个个的配置单元表示，将所有这些配置单元组合为一个目标(target)，就表示需要将这些配置单元全部执行一遍以便进入目标所代表的系统运行状态。 (例如：multi-user.target 相当于在传统使用 SysV 的系统中运行级别 5)
    
    Timer Unit ：定时器，定时器配置单元用来定时触发用户定义的操作，这类配置单元取代了 atd、crond 等传统的定时服务。

    Snapshot Unit ：Systemd 快照，可以切回某个快照，与 target 配置单元相似，快照是一组配置单元。它保存了系统当前的运行状态。

    Path Unit：文件或路径

    Scope Unit：不是由 Systemd 启动的外部进程

    Slice Unit：进程组

每个配置单元都有一个对应的配置文件，系统管理员的任务就是编写和维护这些不同的配置文件，比如一个 MySQL 服务对应一个 mysql.service 文件。这种配置文件的语法非常简单，用户不需要再编写和维护复杂的 systemv 脚本了。

#### 依赖关系

虽然 systemd 将大量的启动工作解除了依赖，使得它们可以并发启动。但还是存在有些任务，它们之间存在天生的依赖，不能用"套接字激活"(socket activation)、D-Bus activation 和 autofs 三大方法来解除依赖（三大方法详情见后续描述）。比如：挂载必须等待挂载点在文件系统中被创建；挂载也必须等待相应的物理设备就绪。为了解决这类依赖问题，systemd 的配置单元之间可以彼此定义依赖关系。

Systemd 用配置单元定义文件中的关键字来描述配置单元之间的依赖关系。比如：unit A 依赖 unit B，可以在 unit B 的定义中用"require A"来表示。这样 systemd 就会保证先启动 A 再启动 B。

#### Systemd 事务

Systemd 能保证事务完整性。Systemd 的事务概念和数据库中的有所不同，主要是为了保证多个依赖的配置单元之间没有环形引用。比如 unit A、B、C，假如它们的依赖关系为:
图 4, Unit 的循环依赖

存在循环依赖，那么 systemd 将无法启动任意一个服务。此时 systemd 将会尝试解决这个问题，因为配置单元之间的依赖关系有两种：required 是强依赖；want 则是弱依赖，systemd 将去掉 wants 关键字指定的依赖看看是否能打破循环。如果无法修复，systemd 会报错。

Systemd 能够自动检测和修复这类配置错误，极大地减轻了管理员的排错负担。

#### 目标

systemd 用目标（target）替代了运行级别的概念，提供了更大的灵活性，如您可以继承一个已有的目标，并添加其它服务，来创建自己的目标。下表列举了 systemd 下的目标和常见 runlevel 的对应关系：

|命令|功能|
|---|---|
|sudo systemctl list-units --type=target|查询当前target|
|sudo systemctl isolate graphical.target|改变当前target，重启无效|
|sudo systemctl enable multi-user.target|改变启动时默认target|
|sudo systemctl enable kdm.service|graphical是默认target，指定使用的display manager|

**Sysvinit 运行级别和 systemd 目标的对应表**

|Sysvinit 运行级别|Systemd 目标|备注|
|---|---|---|
|0 |runlevel0.target, poweroff.target |关闭系统。|
|1, s, single |runlevel1.target, rescue.target |单用户模式。|
|2, 4 |runlevel2.target, runlevel4.target, multi-user.target |用户定义/域特定运行级别。默认等同于 3。|
|3 |runlevel3.target, multi-user.target |多用户，非图形化。用户可以通过多个控制台或网络登录。|
|5 |runlevel5.target, graphical.target |多用户，图形化。通常为所有运行级别 3 的服务外加图形化登录。|
|6 |runlevel6.target, reboot.target |重启|
|emergency |emergency.target |紧急 Shell|

### Systemd 并发启动原理

Systemd 的开发人员仔细研究了服务之间相互依赖的本质问题，发现所谓依赖可以分为三个具体的类型，而每一个类型实际上都可以通过相应的技术解除依赖关系。

具体来说就是通过 "套接字激活"(socket activation)、D-Bus activation 和 autofs 三大方法来解除依赖关系。

#### 并发启动原理之一：解决 socket 依赖

绝大多数的服务依赖是套接字依赖。

比如服务 A 通过一个套接字端口 S1 提供自己的服务，其他的服务如果需要服务 A，则需要连接 S1。因此如果服务 A 尚未启动，S1 就不存在，其他的服务就会得到启动错误。所以传统地，人们需要先启动服务 A，等待它进入就绪状态，再启动其他需要它的服务。Systemd 认为，只要我们预先把 S1 建立好，那么其他所有的服务就可以同时启动而无需等待服务 A 来创建 S1 了。如果服务 A 尚未启动，那么其他进程向 S1 发送的服务请求实际上会被 Linux 操作系统缓存，其他进程会在这个请求的地方等待。一旦服务 A 启动就绪，就可以立即处理缓存的请求，一切都开始正常运行。

那么服务如何使用由 init 进程创建的套接字呢？

Linux 操作系统有一个特性，当进程调用 fork 或者 exec 创建子进程之后，所有在父进程中被打开的文件句柄 (file descriptor) 都被子进程所继承。套接字也是一种文件句柄，进程 A 可以创建一个套接字，此后当进程 A 调用 exec 启动一个新的子进程时，只要确保该套接字的 close_on_exec 标志位被清空，那么新的子进程就可以继承这个套接字。子进程看到的套接字和父进程创建的套接字是同一个系统套接字，就仿佛这个套接字是子进程自己创建的一样，没有任何区别。

这个特性以前被一个叫做 inetd 的系统服务所利用。Inetd 进程会负责监控一些常用套接字端口，比如 Telnet，当该端口有连接请求时，inetd 才启动 telnetd 进程，并把有连接的套接字传递给新的 telnetd 进程进行处理。这样，当系统没有 telnet 客户端连接时，就不需要启动 telnetd 进程。Inetd 可以代理很多的网络服务，这样就可以节约很多的系统负载和内存资源，只有当有真正的连接请求时才启动相应服务，并把套接字传递给相应的服务进程。

和 inetd 类似，systemd 是所有其他进程的父进程，它可以先建立所有需要的套接字，然后在调用 exec 的时候将该套接字传递给新的服务进程，而新进程直接使用该套接字进行服务即可。

#### 并发启动原理之二：解决 D-Bus 依赖

D-Bus 是 desktop-bus 的简称，是一个低延迟、低开销、高可用性的进程间通信机制。它越来越多地用于应用程序之间通信，也用于应用程序和操作系统内核之间的通信。很多现代的服务进程都使用D-Bus 取代套接字作为进程间通信机制，对外提供服务。比如简化 Linux 网络配置的 NetworkManager 服务就使用 D-Bus 和其他的应用程序或者服务进行交互：邮件客户端软件 evolution 可以通过 D-Bus 从 NetworkManager 服务获取网络状态的改变，以便做出相应的处理。

D-Bus 支持所谓"bus activation"功能。如果服务 A 需要使用服务 B 的 D-Bus 服务，而服务 B 并没有运行，则 D-Bus 可以在服务 A 请求服务 B 的 D-Bus 时自动启动服务 B。而服务 A 发出的请求会被 D-Bus 缓存，服务 A 会等待服务 B 启动就绪。利用这个特性，依赖 D-Bus 的服务就可以实现并行启动。

#### 并发启动原理之三：解决文件系统依赖

系统启动过程中，文件系统相关的活动是最耗时的，比如挂载文件系统，对文件系统进行磁盘检查（fsck），磁盘配额检查等都是非常耗时的操作。在等待这些工作完成的同时，系统处于空闲状态。那些想使用文件系统的服务似乎必须等待文件系统初始化完成才可以启动。但是 systemd 发现这种依赖也是可以避免的。

Systemd 参考了 autofs 的设计思路，使得依赖文件系统的服务和文件系统本身初始化两者可以并发工作。autofs 可以监测到某个文件系统挂载点真正被访问到的时候才触发挂载操作，这是通过内核 automounter 模块的支持而实现的。比如一个 open()系统调用作用在"/misc/cd/file1"的时候，/misc/cd 尚未执行挂载操作，此时 open()调用被挂起等待，Linux 内核通知 autofs，autofs 执行挂载。这时候，控制权返回给 open()系统调用，并正常打开文件。

Systemd 集成了 autofs 的实现，对于系统中的挂载点，比如/home，当系统启动的时候，systemd 为其创建一个临时的自动挂载点。在这个时刻/home 真正的挂载设备尚未启动好，真正的挂载操作还没有执行，文件系统检测也还没有完成。可是那些依赖该目录的进程已经可以并发启动，他们的 open()操作被内建在 systemd 中的 autofs 捕获，将该 open()调用挂起（可中断睡眠状态）。然后等待真正的挂载操作完成，文件系统检测也完成后，systemd 将该自动挂载点替换为真正的挂载点，并让 open()调用返回。由此，实现了那些依赖于文件系统的服务和文件系统本身同时并发启动。

当然对于"/"根目录的依赖实际上一定还是要串行执行，因为 systemd 自己也存放在/之下，必须等待系统根目录挂载检查好。

不过对于类似/home 等挂载点，这种并发可以提高系统的启动速度，尤其是当/home 是远程的 NFS 节点，或者是加密盘等，需要耗费较长的时间才可以准备就绪的情况下，因为并发启动，这段时间内，系统并不是完全无事可做，而是可以利用这段空余时间做更多的启动进程的事情，总的来说就缩短了系统启动时间。

### Systemd 开发

...暂时无需要...

### Systemd 管理

systemd 的主要命令行工具是 systemctl。

多数管理员应该都已经非常熟悉系统服务和 init 系统的管理，比如 service、chkconfig 以及 telinit 命令的使用。

systemd 也完成同样的管理任务，只是命令工具 systemctl 的语法有所不同而已，因此用表格来对比 systemctl 和传统的系统管理命令会非常清晰。

**systemd服务管理**

|命令|功能|
|---|---|
|sudo systemctl is-enabled .service|查询服务是否开机启动|
|sudo systemctl enable .service|开机运行服务|
|sudo systemctl disable .service|取消开机运行|
|sudo systemctl start .service|启动服务|
|sudo systemctl stop .service|停止服务|
|sudo systemctl restart .service|重启服务|
|sudo systemctl reload .service|重新加载服务配置文件|
|sudo systemctl status .service|查询服务运行状态|
|sudo systemctl --failed|显示启动失败的服务|

**Systemd 命令和 sysvinit 命令的对照表**

|Sysvinit 命令|Systemd 命令|备注|
|---|---|---|
|service foo start|systemctl start foo.service|用来启动一个服务 (并不会重启现有的)|
|service foo stop|systemctl stop foo.service|用来停止一个服务 (并不会重启现有的)。|
|service foo restart|systemctl restart foo.service|用来停止并启动一个服务。|
|service foo reload|systemctl reload foo.service|当支持时，重新装载配置文件而不中断等待操作。|
|service foo condrestart|systemctl condrestart foo.service|如果服务正在运行那么重启它。|
|service foo status|systemctl status foo.service|汇报服务是否正在运行。|
|ls /etc/rc.d/init.d/|systemctl list-unit-files --type=service|用来列出可以启动或停止的服务列表。|
|chkconfig foo on|systemctl enable foo.service|在下次启动时或满足其他触发条件时设置服务为启用|
|chkconfig foo off|systemctl disable foo.service|在下次启动时或满足其他触发条件时设置服务为禁用|
|chkconfig foo|systemctl is-enabled foo.service|用来检查一个服务在当前环境下被配置为启用还是禁用。|
|chkconfig –list|systemctl list-unit-files --type=service|输出在各个运行级别下服务的启用和禁用情况|
|chkconfig foo –list|ls /etc/systemd/system/*.wants/foo.service|用来列出该服务在哪些运行级别下启用和禁用。|
|chkconfig foo –add|systemctl daemon-reload|当您创建新服务文件或者变更设置时使用。|
|telinit 3|systemctl isolate multi-user.target (OR systemctl isolate runlevel3.target OR telinit 3)|改变至多用户运行级别。|


**systemd 电源管理命令**

|命令|操作|
|---|---|
|systemctl reboot |重启机器|
|systemctl poweroff |关机|
|systemctl suspend |待机|
|systemctl hibernate |休眠|
|systemctl hybrid-sleep |混合休眠模式（同时休眠到硬盘并待机）|

关机不是每个登录用户在任何情况下都可以执行的，一般只有管理员才可以关机。
正常情况下系统不应该允许 SSH 远程登录的用户执行关机命令。否则其他用户正在工作，一个用户把系统关了就不好了。
为了解决这个问题，传统的 Linux 系统使用 ConsoleKit 跟踪用户登录情况，并决定是否赋予其关机的权限。
现在 ConsoleKit 已经被 systemd 的 logind 所替代。

logind 不是 pid-1 的 init 进程。它的作用和 UpStart 的 session init 类似，但功能要丰富很多，它能够管理几乎所有用户会话(session)相关的事情。logind 不仅是 ConsoleKit 的替代，它可以：

    维护，跟踪会话和用户登录情况。如上所述，为了决定关机命令是否可行，系统需要了解当前用户登录情况，如果用户从 SSH 登录，不允许其执行关机命令；如果普通用户从本地登录，且该用户是系统中的唯一会话，则允许其执行关机命令；这些判断都需要 logind 维护所有的用户会话和登录情况。
    Logind 也负责统计用户会话是否长时间没有操作，可以执行休眠/关机等相应操作。
    为用户会话的所有进程创建 CGroup。这不仅方便统计所有用户会话的相关进程，也可以实现会话级别的系统资源控制。
    负责电源管理的组合键处理，比如用户按下电源键，将系统切换至睡眠状态。
    多席位(multi-seat) 管理。如今的电脑，即便一台笔记本电脑，也完全可以提供多人同时使用的计算能力。多席位就是一台电脑主机管理多个外设，比如两个屏幕和两个鼠标/键盘。席位一使用屏幕 1 和键盘 1；席位二使用屏幕 2 和键盘 2，但他们都共享一台主机。用户会话可以自由在多个席位之间切换。或者当插入新的键盘，屏幕等物理外设时，自动启动 gdm 用户登录界面等。所有这些都是多席位管理的内容。ConsoleKit 始终没有实现这个功能，systemd 的 logind 能够支持多席位。

以上描述的这些管理功能仅仅是 systemd 的部分功能，除此之外，systemd 还负责系统其他的管理配置，比如配置网络，Locale 管理，管理系统内核模块加载等，完整地描述它们已经超出了本人的能力。

```bash
# 查看版本
$ systemctl --version
```

### Systemd 命令

Systemd 并不是一个命令，而是一组命令，涉及到系统管理的方方面面。
3.1 systemctl

systemctl是 Systemd 的主命令，用于管理系统。


    # 重启系统
    $ sudo systemctl reboot

    # 关闭系统，切断电源
    $ sudo systemctl poweroff

    # CPU停止工作
    $ sudo systemctl halt

    # 暂停系统
    $ sudo systemctl suspend

    # 让系统进入冬眠状态
    $ sudo systemctl hibernate

    # 让系统进入交互式休眠状态
    $ sudo systemctl hybrid-sleep

    # 启动进入救援状态（单用户状态）
    $ sudo systemctl rescue

3.2 systemd-analyze

systemd-analyze命令用于查看启动耗时。


    # 查看启动耗时
    $ systemd-analyze                                                                                       

    # 查看每个服务的启动耗时
    $ systemd-analyze blame

    # 显示瀑布状的启动过程流
    $ systemd-analyze critical-chain

    # 显示指定服务的启动流
    $ systemd-analyze critical-chain atd.service

3.3 hostnamectl

hostnamectl命令用于查看当前主机的信息。


    # 显示当前主机的信息
    $ hostnamectl

    # 设置主机名。
    $ sudo hostnamectl set-hostname rhel7

3.4 localectl

localectl命令用于查看本地化设置。


    # 查看本地化设置
    $ localectl

    # 设置本地化参数。
    $ sudo localectl set-locale LANG=en_GB.utf8
    $ sudo localectl set-keymap en_GB

3.5 timedatectl

timedatectl命令用于查看当前时区设置。


    # 查看当前时区设置
    $ timedatectl

    # 显示所有可用的时区
    $ timedatectl list-timezones                                                                                   

    # 设置当前时区
    $ sudo timedatectl set-timezone America/New_York
    $ sudo timedatectl set-time YYYY-MM-DD
    $ sudo timedatectl set-time HH:MM:SS

3.6 loginctl

loginctl命令用于查看当前登录的用户。


    # 列出当前session
    $ loginctl list-sessions

    # 列出当前登录用户
    $ loginctl list-users

    # 列出显示指定用户的信息
    $ loginctl show-user ruanyf

systemctl命令在enable、disable和mask子命令中增加了--now选项，可以实现激活的同时启动服务，取消激活的同时停止服务。
```
[shenlin@t460p /usr/local/src]$ systemctl enable --now mysqld.service
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-unit-files ===
Authentication is required to manage system service or unit files.
Authenticating as: shenlin
Password:
==== AUTHENTICATION COMPLETE ===

==== AUTHENTICATING FOR org.freedesktop.systemd1.reload-daemon ===
Authentication is required to reload the systemd state.
Authenticating as: shenlin
Password:
==== AUTHENTICATION COMPLETE ===

==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to manage system services or units.
Authenticating as: shenlin
Password:
==== AUTHENTICATION COMPLETE ===
```

### Systemd 架构

从上至下依次

第1层：systemd Utilities

    systemctl   journalctl  notify analyze cgls cgtop loginctl nspawn

第2层：systemd Daemons

    systemd journald networkd loginuser session

第2层：systemd Targets

    bootmode basic shutdown reboot

    multi-user

        dbus telephony dlog logind

    graphical

        user-session

    user-session

        display service

第3层：systemd Core

    manager systemd namespace log cgroup dbus

    unit

        service timer mount target snapshot path socket swap

    login

        multiseat inhibit session pam

第4层：systemd Libraries

    dbus-1 libpam libcap libcryptsetup tcpwrapper libaudit libnotify

第5曾：Linux Kernel

    cgroups autofs kdbus


![Systemd 架构](http://www.ruanyifeng.com/blogimg/asset/2016/bg2016030703.png)

### Systemd 小结

在不才作者看来，作为系统初始化系统，systemd 的最大特点有两个：

    令人惊奇的激进的并发启动能力，极大地提高了系统启动速度；
    用 CGroup 统计跟踪子进程，干净可靠。

此外，和其前任不同的地方在于，systemd 已经不仅仅是一个初始化系统了。

Systemd 出色地替代了 sysvinit 的所有功能，但它并未就此自满。因为 init 进程是系统所有进程的父进程这样的特殊性，systemd 非常适合提供曾经由其他服务提供的功能，比如定时任务 (以前由 crond 完成) ；会话管理 (以前由 ConsoleKit/PolKit 等管理) 。仅仅从本文皮毛一样的介绍来看，Systemd 已经管得很多了，可它还在不断发展。它将逐渐成为一个多功能的系统环境，能够处理非常多的系统管理任务，有人甚至将它看作一个操作系统。

好的一点是，这非常有助于标准化 Linux 的管理！从前，不同的 Linux 发行版各行其事，使用不同方法管理系统，从来也不会互相妥协。比如如何将系统进入休眠状态，不同的系统有不同的解决方案，即便是同一个 Linux 系统，也存在不同的方法，比如一个有趣的讨论：如何让 ubuntu 系统休眠，可以使用底层的/sys/power/state 接口，也可以使用诸如 pm-utility 等高层接口。存在这么多种不同的方法做一件事情对像我这样的普通用户而言可不是件有趣的事情。systemd 提供统一的电源管理命令接口，这件事情的意义就类似全世界的人都说统一的语言，我们再也不需要学习外语了，多么美好！

如果所有的 Linux 发行版都采纳了 systemd，那么系统管理任务便可以很大程度上实现标准化。此外 systemd 有个很棒的承诺：接口保持稳定，不会再轻易改动。对于软件开发人员来说，这是多么体贴又让人感动的承诺啊！

## Temp

chkconfig
查询或设置系统服务的运行级别信息
实际操作的是这些儿文件夹：/etc/rc[0-6].d

字体名字前有@的是横向文字
