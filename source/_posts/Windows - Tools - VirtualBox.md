---
title: VirtualBox
categories:
  - Windows
  - Tools
tags:
  - Virtualbox
---

oracle 公司的虚拟机软件 virtualbox 的基本设置和操作。

<!--more-->

http://blog.csdn.net/pkueecser/article/details/8836264

全局设定中可以设置主机键 Host，一般为 [右Ctrl] [左Ctrl] [左Win] 键

进入“自动缩放模式”后没有菜单栏，可以使用 Host+c 键返回

## 增强功能

### 增强功能简介

VBox 增强功能 Guest Additions，就是一系列要安装到客户机上的设备驱动和系统应用，
作用就是提高客户机的性能和提升使用体验

一些儿 linux 的发行版已经默认包含了此增强功能，但最好是使用 vbox 自带的版本，安
装时，Guest Additions 会自动检测和替换发行版自带的版本，但最好在替换前做一个快照
。

在安装GuestAddition之间，需要让CentOS为编译生成外置核心模块做好准备。

(Before installing the Guest Additions, you will have to prepare your guest
system for building external kernel modules.)

#### 安装 linux 外置核心模块

```
//查看内核版本
$ uname -r

//查看安装的内核和版本
$ rpm -qa | grep kernel

//查看服务器上 kernel-devel 版本
//如果和上面的内核版本不一样，则需要升级内核版本
$ yum list|grep kernel-devel

//更新系统所有软件到最新版本
//清理步骤执行到最后时耗费时间较长
$ sudo yum update
正在升级   : binutils-2.20.51.0.2-5.47.el6_9.1.i686  
正在升级   : sudo-1.8.6p3-29.el6_9.i686 
清理       : curl-7.19.7-52.el6.i686 
清理       : libcurl-7.19.7-52.el6.i686 
Verifying  : nss-sysinit-3.27.1-13.el6.i686
Verifying  : sudo-1.8.6p3-27.el6.i686
已安装:
  kernel.i686 0:2.6.32-696.3.2.el6
更新完毕:
binutils.i686 0:2.20.51.0.2-5.47.el6_9.1 curl.i686 0:7.19.7-53.el6_9                       

//安装外置核心模块
$ sudo yum install -y kernel-devel
```

#### 安装依赖包

如果 CentOS 版本早于 6，需要在 /etc/grub.conf 中添加一行 divider=10，将这个参数
传递给核心以减少 idle CPU load。

```
//先更新是为了保证 kernel 和 kernel-devel 版本号一致
$ yum update
$ yum install kernel-headers
$ yum install kernel-devel
$ yum install gcc* 
$ yum install make
```

#### 安装增强功能

挂载镜像文件 VBoxGuestAdditions.iso
```
sudo mount /dev/cdrom  /mnt/cdrom
```

执行安装命令
```
$ cd /mnt/cdrom
$ sudo sh ./VBoxLinuxAdditions.run
```

#### 安装问题

查看安装日志，有错误
```
$ vi /var/log/vboxadd-install.log

echo "  ERROR: Kernel configuration is invalid.";               \
echo "         include/linux/autoconf.h or include/config/auto.conf are missing.";      \
echo "         Run 'make oldconfig && make prepare' on kernel src to fix it.";  \
```

但上面所说的缺少的配置文件都存在
```
>> ll /usr/src/kernels/2.6.32-696.3.2.el6.i686/include/linux/autoconf.h
-rw-r--r--. 1 root root 117016 6月  20 08:54 /usr/src/kernels/2.6.32-696.3.2.el6.i686/include/linux/autoconf.h


>> ll /usr/src/kernels/2.6.32-696.3.2.el6.i686/include/config/auto.conf
-rw-r--r--. 1 root root 112820 6月  20 08:54 /usr/src/kernels/2.6.32-696.3.2.el6.i686/include/config/auto.conf
```

**Question:**

    上面的问题没解决，可能是要编译 linux 内核

#### 设置共享

(1) 设备->分配数据空间

(2) 挂载/卸载命令
```
mkdir /mnt/share
mount -t vboxsf myshare /mnt/share/
umount -f /mnt/share
```
(3)开机自动共享
```
vim /etc/fstab
```
添加一项
```
myshare /mnt/share vboxsf rw,gid=100,uid=1000,auto 0 0
```

### 增强功能作用

（1）在客户机上安装了一个鼠标驱动，实现了客户机和主机间鼠标无缝移动，不用再按
Host 键来释放鼠标

（2）文件共享：可以把主机的一个文件夹作为共享文件夹来让客户机访问

（3）安装虚拟显卡驱动，实现2D和3D视频图形加速，自动调整客户机分辨率

（4）无缝窗口，客户机的窗口最大化，桌面背景不显示，将直接显示宿主机的桌面背景，
使用 Host+L 键切换

（5）实现了宿主机/客户机之间的交流通道，使用基于字符串的数据交换机制，可以让宿主
机控制和监控客户机，也可以在宿主机启动客户机中的程序

（6）更好的实现了客户机和宿主机之间的时间同步。
由于各种原因，宿主机和客户机直接的时间差会要求很小，但客户机可以被暂停，这时客户
机的时间也将暂停流动，如果几分钟或几个小时后恢复，则主机和客户机都有时间差，当主
机和客户机时间差较小时，vbox将会逐渐小幅度的调整客户机的时间，当主机和客户机的时
间差较大时（几小时），vbox则会一步到位的调整好客户机时间。

（7）与主机共享剪贴板的内容，也就是说直接可以在主机、客户机之间复制、粘贴（不支
持文件）

（8）自动登录客户机系统

增强功能最好和 virtualbox 保持同步，升级时一起升级，否则增强功能版本较老时会提示
升级，

也可以通过设置`/VirtualBox/GuestAdd/CheckHostVersion  0` 去掉升级提示

## 常用命令

查看虚拟主机
```
VBoxManage list vms
```

查看硬盘
```
VBoxManage list hdds
```

直接复制的硬盘会提示UUID重复，使用下面的命令重新生成UUID则可以
```
VBoxManage internalcommands sethduuid "D:\CentOS.vmdk"
```

转换格式，转换时会自动压缩
```
VBoxManage clonehd "source.vmdk" "cloned.vdi" --format vdi
```

压缩 vdi 磁盘，不支持 vmdk 磁盘压缩
```
VBoxManage modifyhd mydisk.vdi --compact
```

调整虚拟磁盘大小为 8G，单位为 MB
```
//老版本
VBoxManage modifyhd "D:\CentOS.vdi" --resize 10240

//新版本
VBoxManage modifymedium "D:\CentOS.vdi" --resize 10240
```
* virtualbox 有固定大小和动态调整两种硬盘类型
* 固定大小的是不能再改变的，这里改动的是动态调整硬盘的大小
* 而且只能增大，不能缩小
* 当创建硬盘时，即使选择的是动态调整的格式，也是需要指定占用空间的上限
* 这里修改的就是这个上限值，而且是一个逻辑值，不是真的去改动实际硬盘文件的大小
* 即使是逻辑值，修改完之后，仍然需要使用下面的 gparted 软件把新增加的空间分配到
  分区
* 现在的问题是还没到空间上限，virtualbox就不自动分配空间了为什么？


vmware 软件可以直接调整 vmdk 格式硬盘
```
vmware-vdiskmanager -x 70Gb "E:\Red Hat Linux\Red Hat Linux.vmdk"
```

调整分区空间：

    * 上述操作只是给虚拟磁盘增加了空间，但增加的空间并未分配到分区
    * 无损数据的扩展分区空间使用 GParted 软件
    （下载：http://gparted.sourceforge.net/download.php）
    * 加载gaprted.iso并设定从光盘启动
    
    
    http://www.linuxidc.com/Linux/2013-12/93995.htm


无窗口启动虚拟机
```
VBoxManage startvm "CentOS" --type headless
```

映射宿主主机端口到虚拟机端口
```
VBoxManage modifyvm "CentOS" --natpf1 "guestssh,tcp,,2222,,22"
VBoxManage modifyvm "CentOS" --natpf1 "guesthttp,tcp,,80,,80"
```

自带增强包：好像是为了解决分辨率太低的问题
windows 镜像文件路径
```
C:\Program Files\Oracle\VirtualBox\VBoxGuestAdditions.iso
```
linux 加载路径
```
/media/username/VBoxGuestAdditions_x.xx.xx
```
linux 安装
```
cd /media/username/VBoxGuestAdditions_x.xx.xx
sudo sh ./VBoxLinuxAdditions.run
```


