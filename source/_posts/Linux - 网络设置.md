# linux 网络设置

标签（空格分隔）： AAA linux ifconfig

---

http://www.cnblogs.com/Jtianlin/p/4655831.html
http://xtbao.blog.51cto.com/7482722/1671739
http://www.iyunv.com/thread-269695-1-1.html
http://www.linuxidc.com/Linux/2013-08/88809.htm

## 网络基本命令

```
//网卡信息配置文件
$ sudo vi /etc/udev/rules.d/70-persistent-net.rules

# PCI device 0x8086:0x100f (e1000)
SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="eth0-MAC", ATTR{type}=="1", KERNEL=="eth*", NAME="eth0"

# PCI device 0x8086:0x100f (e1000)
SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="eth1-MAC", ATTR{type}=="1", KERNEL=="eth*", NAME="eth1"

//
$ sudo vi /etc/sysconfig/network-scripts/ifcfg-eth0

//查看所有网卡
$ ifconfig -a

//
$ ifconfig eth0

//
$ sudo ifup ifcfg-eth0
```

windows 网络命令
```
netstat -ano | findstr 2222
```

## 进程

```
>> ps -ef|grep httpd
ssy       2001  1546 21 10:37 pts/1    00:00:00 grep httpd

>> netstat -le

>> service httpd status
httpd: 未被识别的服务

>> sudo /usr/local/httpd-2.4.25/bin/apachectl start
```

## VirtualBox 导致的问题

device eth0 does not seem to present

```
>> ifconfig eth3
eth3: error fetching interface information: Device not found
```

在KVM中克隆出新的CentOS虚拟机时，出现如下问题：
```
service network restart
Shutting down loopback insterface: [ OK ]
Bringing up loopback insterface: [ OK ]
Bringing up interface eth0: Device eth0 does not seem to be present,delaying initialization. [FAILED]
```

用ifconfig查看发现缺少eth0，只有lo；用ifconfig -a查看发现多出了eth1的信息。

解决办法1：
```
mv /etc/sysconfig/network-scripts/ifcfg-eth0  /etcsysconfig/network-scripts/ifcfg-eth1
```
将eth0的mac地址改为eth1的mac地址，同时改变其DEVICE名称为eth1，再重启网络即可。

解决办法2：
```
rm -rf /etc/udev/rules.d/70-persistent-net.rules
reboot
```

总之，只要保证/etc/sysconfig/network-scripts/ifcfg-eth0 与/etc/udev/rules.d/70-persistent-net.rules的信息一致即可，即网卡地址与网卡编号一致，这样service network restart 就可以配置成功。

## NetworkManager

查看 nmcli 命令由哪个软件包提供
```
$ yum provides "*/nmcli"
```
安装对应的软件包
```
$ yum -y install NetworkManager
```

## uuid

不小心将/etc/sysconfig/network-scripts/ifcfg-eth0(可以通过此文件进行查看UUID)删除或者损坏，要重新编辑ifcfg-eth0文件时不知道网卡的UUID是什么(当然也可以不写)，那我们还有什么方法可以查看网卡的UUID呢？在这里我们使用的方法是使用nmcli命令查看
```
nmcli con | sed -n '1,2p'
```

## uuidgen

-t 每次后面都是网卡的MAC地址
-r 每次全变
不加任何参数则默认是 -r 
````
>> uuidgen -t eth0
209997dc-61e1-11e7-8670-08002708bab0

ssy@e42.matrix ~
>> uuidgen -t eth0
245c40ae-61e1-11e7-a5f3-08002708bab0

ssy@e42.matrix ~
>> uuidgen -t eth0
27e4ea82-61e1-11e7-bd8f-08002708bab0

ssy@e42.matrix ~
>> uuidgen -r eth0
d02d1ef5-2b35-41bc-a9b1-f854d8cf29d5

ssy@e42.matrix ~
>> uuidgen -r eth0
7ce3c5ef-b172-4643-8b78-961ce4fb0ecc

ssy@e42.matrix ~
>> uuidgen -r eth0
7d39b85e-f748-40d7-a6db-979ad1d150bc
```

## dbus

dbus-daemon: D-Bus的后台进程，作为D-Bus的消息中转枢纽，负责消息的转发，可分成system和session两种。

dbus-launch 命令用于从shell脚本启动一个 dbus-daemon 的会话总线实例. 
dbus-launch 命令通常会在用户的登录脚本中调用. 
dbus-launch: 启动一个dbus-daemon，后面有不同的参数。
当系统启动时，需要使用dbus-launch来启动dbus-daemon
```
eval `dbus-launch --auto-syntax`
```

问题1

    $ gdm
    ** (gdm-binary:2195): WARNING **: Couldn't connect to system bus: Failed to connect to socket /var/run/dbus/system_bus_socket: No such file or directory
    
    我用ntsysv查看下了发现messagebus没有开机启动，设置messagebus自动启动后重启，再使用gdm就可以启动桌面了。

问题2

    ssh Client failed to connect to the D-BUS daemon
    
    Add the following lines to ~/.bashrc
    export $(dbus-launch)
    export NSS_USE_SHARED_DB=ENABLED

## nmcli

1、首先我们查看一下nmcli是哪个软件包提供的
```
[root@huis ~]# yum provides "*/nmcli"
Loaded plugins：fastestmirror, security
Loading mirror speeds from cached hostfile
 * base: mirrors.cug.edu.cn
 * extras: mirrors.cug.edu.cn
 * updates: centos.ustc.edu.cn
1:NetworkManager-0.8.1-75.el6.i686 : Network connection manager and
                                   : user applications
Repo        : base
Matched from:
Filename    : /usr/bin/nmcli
```

2、从上面结果可以看出nmcli，接下来我们安装NetworkManager这个软件包
```
[root@huis ~]# yum -y install NetworkManager
```

3、启动NetworkManager服务
```
[root@huis ~]# sudo service NetworkManager start
Setting network parameters...                      [  OK  ]
Starting NetworkManager daemon:                    [  OK  ]
```
* 是不是 sudo 执行 service 是有区别的
* 不使用 sudo 不会有错误提示，但没有真正运行

4、NetWorkManager 和 network 冲突？

```
>> service NetworkManager status
NetworkManager 已死，但 pid 文件仍存

>> sudo rm /var/run/NetworkManager/NetworkManager.pid 

>> service NetworkManager status
NetworkManager 已死，但是 subsys 被锁

>> sudo rm -rf /var/lock/subsys/NetworkManager
```

9、nmcli 和 dbus
```
>> nmcli con
错误：无法连接到 D-Bus。

>> nmcli dev

** (process:1692): WARNING **: Couldn't connect to system bus: Failed to connect to socket /var/run/dbus/system_bus_socket: 没有那个文件或目录
错误：无法连接到网络管理器。

>> yum list|grep dbus
dbus.i686                                  1:1.2.24-8.el6_6            @base    
dbus-glib.i686                             0.86-6.el6                  @anaconda-CentOS-201703281202.i386/6.9
dbus-libs.i686                             1:1.2.24-8.el6_6            @anaconda-CentOS-201703281202.i386/6.9
eggdbus.i686                               0.6-3.el6                   @base    
dbus-c++.i686                              0.5.0-0.10.20090203git13281b3.1.el6
dbus-c++-devel.i686                        0.5.0-0.10.20090203git13281b3.1.el6
dbus-devel.i686                            1:1.2.24-8.el6_6            base     
dbus-doc.noarch                            1:1.2.24-8.el6_6            base     
dbus-glib-devel.i686                       0.86-6.el6                  base     
dbus-python.i686                           0.83.0-6.1.el6              base     
dbus-python-devel.i686                     0.83.0-6.1.el6              base     
dbus-qt.i686                               0.70-7.2.el6                base     
dbus-qt-devel.i686                         0.70-7.2.el6                base     
dbus-x11.i686                              1:1.2.24-8.el6_6            base     
eggdbus-devel.i686                         0.6-3.el6                   base     
python-slip-dbus.noarch                    0.2.20-1.el6_2              base     
sssd-dbus.i686                             1.13.3-56.el6               base     

>> yum info dbus
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.nju.edu.cn
 * extras: mirrors.nwsuaf.edu.cn
 * updates: mirrors.nwsuaf.edu.cn
已安装的软件包
Name        : dbus
Arch        : i686
Epoch       : 1
Version     : 1.2.24
Release     : 8.el6_6
Size        : 486 k
Repo        : installed
From repo   : base
Summary     : D-BUS message bus
URL         : http://www.freedesktop.org/software/dbus/
License     : GPLv2+ or AFL
Description : D-BUS is a system for sending messages between applications. It is
            : used both for the system-wide message bus service, and as a
            : per-user-login-session messaging facility.


>> yum info dbus-x11
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.nju.edu.cn
 * extras: mirrors.nwsuaf.edu.cn
 * updates: mirrors.nwsuaf.edu.cn
可安装的软件包
Name        : dbus-x11
Arch        : i686
Epoch       : 1
Version     : 1.2.24
Release     : 8.el6_6
Size        : 39 k
Repo        : base
Summary     : X11-requiring add-ons for D-BUS
URL         : http://www.freedesktop.org/software/dbus/
License     : GPLv2+ or AFL
Description : D-BUS contains some tools that require Xlib to be installed, those are
            : in this separate package so server systems need not install X.


>> rpm -qa|grep dbus
dbus-libs-1.2.24-8.el6_6.i686
dbus-1.2.24-8.el6_6.i686
eggdbus-0.6-3.el6.i686
dbus-glib-0.86-6.el6.i686


>> rpm -ql dbus
/bin/dbus-cleanup-sockets
    /bin/dbus-daemon
/bin/dbus-monitor
/bin/dbus-send
/bin/dbus-uuidgen
/etc/dbus-1
/etc/dbus-1/session.conf
/etc/dbus-1/session.d
/etc/dbus-1/system.conf
/etc/dbus-1/system.d
/etc/rc.d/init.d/messagebus
/lib/dbus-1
/lib/dbus-1/dbus-daemon-launch-helper
/usr/share/dbus-1
/usr/share/dbus-1/interfaces
/usr/share/dbus-1/services
/usr/share/dbus-1/system-services
/usr/share/doc/dbus-1.2.24
/usr/share/doc/dbus-1.2.24/COPYING
/usr/share/man/man1/dbus-cleanup-sockets.1.gz
/usr/share/man/man1/dbus-daemon.1.gz
/usr/share/man/man1/dbus-monitor.1.gz
/usr/share/man/man1/dbus-send.1.gz
/usr/share/man/man1/dbus-uuidgen.1.gz
/var/lib/dbus
/var/run/dbus

>> dbus-daemon
No configuration file specified.
dbus-daemon [--version] [--session] [--system] [--config-file=FILE] [--print-address[=DESCRIPTOR]] [--print-pid[=DESCRIPTOR]] [--fork] [--nofork] [--introspect]

>> dbus-daemon --system --print-pid --print-address
Failed to start message bus: Failed to bind socket "/var/run/dbus/system_bus_socket": Permission denied

>> sudo dbus-daemon --system --print-pid --print-address
[sudo] password for ssy: 
unix:path=/var/run/dbus/system_bus_socket,guid=6100e9c5c906f107cd0f3efb00001480
1797

>> ll /var/run/dbus/
总用量 0
srwxrwxrwx. 1 root root 0 7月   6 09:13 system_bus_socket

>> nmcli con show
错误：无法获得连接：设定服务没有运行。

>> nmcli dev

** (process:1802): WARNING **: nm_client_get_devices: error getting devices: The name org.freedesktop.NetworkManager was not provided by any .service files

设备       类型              状态        

>> sudo /etc/rc.d/init.d/messagebus start
[sudo] password for ssy: 
启动系统消息总线：

>> nmcli con
错误：无法获得连接：设定服务没有运行。

>> sudo yum install -y dbus-x11

>> rpm -ql dbus-x11
/etc/X11/xinit/xinitrc.d/00-start-message-bus.sh
/usr/bin/dbus-launch
/usr/share/man/man1/dbus-launch.1.gz
```

