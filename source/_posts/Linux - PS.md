---
title: linux - ps
categories:
  - Linux
tags:
  - Linux
  - ps
---

https://www.linux.org/docs/man1/ps.html

ps命令是Process Status的缩写，用来列出当前进程的快照，就是执行ps命令的那个时刻的进程状态，

如果想要动态连续的显示进程信息，可以使用top命令。

Linux上进程有5种状态:

    1. 运行(正在运行或在运行队列中等待)
    2. 中断(休眠中, 受阻, 在等待某个条件的形成或接受到信号)
    3. 不可中断(收到信号不唤醒和不可运行, 进程必须等待直到有中断发生)
    4. 僵死(进程已终止, 但进程描述符存在, 直到父进程调用wait4()系统调用后释放)
    5. 停止(进程收到SIGSTOP, SIGSTP, SIGTIN, SIGTOU信号后停止运行运行)

ps工具标识进程的5种状态码:

    D 不可中断 uninterruptible sleep (usually IO)
    R 运行 runnable (on run queue)
    S 中断 sleeping
    T 停止 traced or stopped
    Z 僵死 a defunct (”zombie”) process

    注: 其它状态还包括W(无驻留页), `<`(高优先级进程), N(低优先级进程), L(内存锁页).


3．命令参数：

    a  显示所有进程

    -a 显示同一终端下的所有程序

    -A 显示所有进程

    c  显示进程的真实名称

    -N 反向选择

    -e 等于“-A”

    e  显示环境变量

    f  显示程序间的关系

    -H 显示树状结构

    r  显示当前终端的进程

    T  显示当前终端的所有程序

    u  指定用户的所有进程

    -au 显示较详细的资讯

    -aux 显示所有包含其他使用者的行程 

    -C<命令> 列出指定命令的状况

    --lines<行数> 每页显示的行数

    --width<字符数> 每页显示的字符数

    --help 显示帮助信息

    --version 显示版本显示

ps 命令的常用方法：`ps aux` 和 `ps -ef`

```bash
[shenlin@t460p ~]$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.5  0.8 128164  6812 ?        Ss   07:52   0:01 /usr/lib/systemd/systemd --switched-root --system --deserialize 21
root         2  0.0  0.0      0     0 ?        S    07:52   0:00 [kthreadd]
root         3  0.0  0.0      0     0 ?        S    07:52   0:00 [ksoftirqd/0]
root         5  0.0  0.0      0     0 ?        S<   07:52   0:00 [kworker/0:0H]
root         7  0.0  0.0      0     0 ?        S    07:52   0:00 [migration/0]
postfix   1051  0.0  0.5  89648  3996 ?        S    07:53   0:00 pickup -l -t unix -u
postfix   1052  0.0  0.5  89716  4016 ?        S    07:53   0:00 qmgr -l -t unix -u
root      1056  0.0  0.6 145704  5252 ?        Ss   07:53   0:00 sshd: shenlin [priv]
shenlin   1067  0.1  0.2 145704  2256 ?        S    07:53   0:00 sshd: shenlin@pts/0
shenlin   1068  0.0  0.2 115392  2016 pts/0    Ss   07:53   0:00 -bash
shenlin   1093  0.0  0.2 151064  1800 pts/0    R+   07:57   0:00 ps aux
```
USER：用户名称
PID：进程号
%CPU：进程占用CPU的百分比
%MEM：进程占用物理内存的百分比
VSZ：进程占用的虚拟内存大小（单位：KB）
RSS：常驻集大小（单位：KB）
    该进程占用的固定內存量（KB）（驻留中页的数量）
    驻留集的大小，可以理解为内存中页的数量
    VSZ表示如果一个程序完全驻留在内存的话需要占用多少内存空间，而RSS指明了当前实际占用了多少内存。
TT：终端名称（缩写），若为？，则代表此进程与终端无关，因为它们是由系统启动的
    TTY域有个"？"是因为这些程序或者在系统启动的时候就开始运行了，或者是由初始化脚本（init script）启动的。这些进程没有控制终端，所以作了标记。
    pts/N表示这个命令是远程连接运行的，有与其关联的一个终端。当你的机器开放了多条连接，而你又想确定某个命令运行在哪个窗口的时候，这个信息是很有用的。
STAT：进程状态，
    其中S-睡眠，s-表示该进程是会话的先导进程，N-表示进程拥有比普通优先级更低的优先级，R-正在运行，D-短期等待，Z-僵死进程，T-被跟踪或者被停止等等
    D      //无法中断的休眠状态（通常 IO 的进程）；
    R      //正在运行可中在队列中可过行的；
    S      //处于休眠状态；
    T      //停止或被追踪；
    W      //进入内存交换 （从内核2.6开始无效）；
    X      //死掉的进程 （基本很少见）；
    Z      //僵尸进程；
    `<`    //优先级高的进程
    N      //优先级较低的进程
    L      //有些页被锁进内存；
    s      //进程的领导者（在它之下有子进程）；
    l      //多线程，克隆线程（使用 CLONE_THREAD, 类似 NPTL pthreads）；
    `+`    //位于后台的进程组；
STARTED：进程的启动时间
TIME：CPU时间，即进程使用CPU的总时间
COMMAND：启动进程所用的命令和参数，如果过长会被截断显示 

```bash
[shenlin@t460p ~]$ ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 07:52 ?        00:00:01 /usr/lib/systemd/systemd --switched-root --system --deserialize 21
root         2     0  0 07:52 ?        00:00:00 [kthreadd]
root         3     2  0 07:52 ?        00:00:00 [ksoftirqd/0]
root         5     2  0 07:52 ?        00:00:00 [kworker/0:0H]
postfix   1051  1050  0 07:53 ?        00:00:00 pickup -l -t unix -u
postfix   1052  1050  0 07:53 ?        00:00:00 qmgr -l -t unix -u
root      1056   948  0 07:53 ?        00:00:00 sshd: shenlin [priv]
shenlin   1067  1056  0 07:53 ?        00:00:00 sshd: shenlin@pts/0
shenlin   1068  1067  0 07:53 pts/0    00:00:00 -bash
root      1094     2  0 08:00 ?        00:00:00 [kworker/0:0]
root      1108     1  0 08:01 ?        00:00:00 /usr/sbin/anacron -s
shenlin   1111  1068  0 08:01 pts/0    00:00:00 ps -ef
```
UID：用户ID
PID：进程ID
PPID：父进程ID
C：CPU用于计算执行优先级的因子。数值越大，表明进程是CPU密集型运算，执行优先级会降低；数值越小，表明进程是I/O密集型运算，执行优先级会提高
STIME：进程启动的时间
TTY：完整的终端名称
TIME：CPU时间
CMD：完整的启动进程所用的命令和参数


"ps -aux"不同于"ps aux"。

    按照POSIX和UNIX的标准，"ps -aux"的-a选项表示所有进程，-ux选项表示用户"x"进程。如果用户名为"x"不存在，ps将会解释为"ps aux"

    存在这种情况是为了帮助转换旧的脚本和习惯，不稳定不可依赖。

    Note that "ps -aux" is distinct from "ps aux".

    The POSIX and UNIX standards require that "ps -aux" print all processes owned by a user named "x", 
    as well as printing all processes that would be selected by the -a option.

    If the user named "x" does not exist, this ps may interpret the command as "ps aux" instead and print
    a warning.

    This behavior is intended to aid in transitioning old scripts and habits.  It is fragile, subject
    to change, and thus should not be relied upon.

"ps aux"和"ps -ef"。

    -ef是System V展示风格，而aux是BSD风格。
    COMMADN列如果过长，aux会截断显示，用ps aux | grep bmgctl 来过滤进程，可能会获取不到bmgctl进程，而ef显示出来就是带全路径的进程名，会显示出bmgctl

综上：
如果想查看进程的CPU占用率和内存占用率，可以使用aux
如果想查看进程的父进程ID和完整的COMMAND命令，可以使用-ef


rss        RSS      resident set size, the non-swapped physical memory that a task has used (in kiloBytes). (alias rssize, rsz).


vsz        VSZ      virtual memory size of the process in KiB (1024-byte units). Device mappings are currently excluded; this is subject
                    to change. (alias vsize).


size       SZ       approximate amount of swap space that would be required if the process were to dirty all writable pages and then be
                    swapped out. This number is very rough!


sz         SZ       size in physical pages of the core image of the process. This includes text, data, and stack space. Device mappings
                    are currently excluded; this is subject to change. See vsz and rss.

This version of ps accepts several kinds of options:

     1   UNIX options, which may be grouped and must be preceded by a dash.
     2   BSD options, which may be grouped and must not be used with a dash.
     3   GNU long options, which are preceded by two dashes.

EXAMPLES

    To see every process on the system using standard syntax:
      ps -e
      ps -ef
      ps -eF
      ps -ely

    To see every process on the system using BSD syntax:
      ps ax
      ps axu

    To print a process tree:
      ps -ejH
      ps -U root -u root u

    To see every process with a user-defined format:
      ps -eo pid,tid,class,rtprio,ni,pri,psr,pcpu,stat,wchan:14,comm
      ps axo stat,euid,ruid,tty,tpgid,sess,pgrp,ppid,pid,pcpu,comm
      ps -Ao pid,tt,user,fname,tmout,f,wchan

    Print only the process IDs of syslogd:
      ps -C syslogd -o pid=

    Print only the name of PID 42:
      ps -q 42 -o comm=

    实例1：显示所有进程信息 
    命令： ps -A

    实例2：显示指定用户信息 
    命令： ps -u root

    实例3：显示所有进程信息，连同命令行 
    命令： ps -ef

    实例4： ps 与grep 常用组合用法，查找特定进程
    命令： ps -ef|grep ssh
    
    实例5：将目前属于您自己这次登入的 PID 与相关信息列示出来
    命令： ps -l
    F 代表这个程序的旗标 (flag)， 4 代表使用者为 super user

        [root@localhost test6]# ps -l
        F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
        4 S     0 17398 17394  0  75   0 - 16543 wait   pts/0    00:00:00 bash
        4 R     0 17469 17398  0  77   0 - 15877 -      pts/0    00:00:00 ps

        S 代表这个程序的状态 (STAT)，关于各 STAT 的意义将在内文介绍 
        UID 程序被该 UID 所拥有 
        PID 就是这个程序的 ID ！ 
        PPID 则是其上级父程序的ID 
        C CPU 使用的资源百分比 
        PRI 这个是 Priority (优先执行序) 的缩写，详细后面介绍 
        NI 这个是 Nice 值，在下一小节我们会持续介绍 
        ADDR 这个是 kernel function，指出该程序在内存的那个部分。如果是个 running的程序，一般就是 "-" 
        SZ 使用掉的内存大小 
        WCHAN 目前这个程序是否正在运作当中，若为 - 表示正在运作

    实例6：列出目前所有的正在内存当中的程序
    命令： ps aux

    实例7：列出类似程序树的程序显示
    命令： ps -axjf

    实例8：找出与 cron 与 syslog 这两个服务有关的 PID 号码
    命令： ps aux | egrep '(cron|syslog)'
    
    3. 输出指定的字段
    命令： ps -o pid,ppid,pgrp,session,tpgid,comm
