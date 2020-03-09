---
title: Linux - 文件系统管理
categories:
  - Linux
tags:
  - Linux
---

Linux 文件系统管理

<!--more-->

【文件系统构成】

/usr/bin /bin	存放所有用户可以执行的命令
/usr/sbin /sbin 存放只有系统管理员(如root)可以执行的命令，普通用户无权访问

???
上面两个目录存放的文件都是 single user mode 模式下可以使用的命令
single user mode 是一个特殊的模式，允许你以 root 的身份登录系统，以进行系统修复、
更新或测试，此模式下通常因为安全问题网络都是禁用的
在linux下安装的程序，通常都不会放到这两个目录

/root
root 用户的家目录，不同于普通用户，root 用户的家目录不位于 /home 目录下，这样也
确保了 root 用户始终可以访问，避免你把普通用户的家目录放到无法访问的磁盘上造成的
问题

/snap 用于 snap 包，主要是 ubuntu 发行版使用，snap 程序是一种自包含程序，运行方
式和普通包不同

/srv 服务进程保存数据的目录，如运行的 web 服务器、ftp 服务器等，则被互联网用户访
问的网页文件和 ftp
文件应该保存在这里，

/home 用户缺省宿主目录，一些用户把此目录挂载到其他分区或其他硬盘，可以在重装系统
时保持用户数据

/run 一个相当新的目录，使用的是 tempfs 文件系统，即内存盘，关机或重启后此目录的
数据就消失，用于让系统启动时的进程存储运行时信息

/sys 存在了很长时间的一个目录，和内核交互的一种方式，和 /run 目录一样，不是实际
的写入了硬盘，而是内存盘

/proc 虚拟文件系统，存放当前内存镜像（缺省是保存在内存镜像中的）
和 /dev 目录类似，里面都是伪文件，实际是系统进程

/dev 硬件设备在linux下的文件表示，
如 /dev/sda 是第一块硬盘
   /dev/sda1 是第一块硬盘的第一个分区
类似于 Windows 的资源管理器，普通用户应避免涉及

/lib 存放系统程序运行所需的共享库
/lib32
/lib64

/lost+found 存放一些儿系统出错的检查结果

/tmp 存放会话使用的临时文件，如文本编辑程序尚未保存的内容等，如程序不正常崩溃，
可以在这里找找是否有自动备份，通常此目录在系统启动后是空的，如果有文件，则表明系
统无法移除它们，此时可以使用 root 用户以 single user mode 登录后删除

/etc 备份系统时重要备份目录，存放系统级别的配置文件，注意系统级别是相对于个人级别
设置而言，有的软件如 Libre Office 是每个人一个配置文件，则这样软件的配置文件不会
位于此目录下

由 unix 系统的关键开发者 Dennies Ritchie 确认 etc 最初是拉丁文 `et cetera` 的缩
写，含义是 ”...等“，最初此目录就是用来存放那些放其他目录不合适的东西，现在此目
录的用途已变为存放系统的配置文件
> I assure you that the original contents of /etc were the "et cetera" that didn't
> seem to fit elsewhere. Other variants might do their own etymologies differently.
>
> Regards, Dennis

/var 包含经常发生变动的文件，占用空间会增长的文件或目录，如邮件、日志文件、计划
任务等
/var/crash 系统崩溃的


/usr 存放所有命令、库和手册页 类似于c:\windows
Unix System Resource
universal system resources
用户使用的应用程序的安装目录，此处安装的程序被认为对基本的系统操作是非必要的
/bin /sbin 则是系统和系统管理员使用的程序的安装目录，
此处安装的程序通常会分布到几个子目录，如
/usr/bin
/usr/sbin
/usr/local/bin
/usr/local/sbin
通常还包括它们使用的库文件：
/usr/lib
/usr/local/lib
大多数通过源码编译安装的软件都位于：
/usr/local/local
大多数比较大的程序会把自己安装到：
/usr/local/share
安装程序的源文件和头文件通常会安装到：
/usr/local/src


## mnt 和 media

/mnt 临时挂载点
系统管理员根据需要用来临时挂载文件系统
从传统到现在都是用作临时挂载点
不能用于安装程序时做临时目录

/media 此目录下的子目录为可移动媒体(光盘、软盘、u盘、网络硬盘)的挂载点
大多数发行版都会**自动**为用户把**外部**存储设备挂载到此目录下
如你插入的 U 盘位于 `/media/YourUserName/DeviceName` 目录下

直接把可移动媒体挂载到根目录下会导致大量的额外目录
历史上，还使用过 /mnt, /cdrom /mnt/cdrom 作为可移动媒体的挂载点
现在使用 /mnt 下的子目录作为移动媒体挂载点也很常见，不过和 /mnt 作为临时挂载点的
老传统冲突

临时挂载点和可移动媒体挂载点，没有很严格的标准，如 U 盘可以挂载上能访问就可以：
* 如果你手动挂载目录，则自己使用 mnt 目录，让系统管理 media 目录
* 一些发行版和文件管理程序如 nautilus 还会列出一个 OtherLocations 挂载临时文件系统

## others

/opt 一些软件会放这里，还可以放自己写的程序 
???

/boot 内核文件及自举程序文件保存位置，备份系统时重要备份目录，bootloader
/usr/local 第三方程序安装目录，类似于c：\programe files
/cdrom 是一个传统的光驱挂载点，并非所有的发行版都有此目录

df 查看linnux的分区情况
	-h
	-m
du 查看文件、目录大小
	-h
	-s 目录
fsck e2fsck(file system check) 检测修复文件系统，单用户模式执行
	e2fsck -p 自动修复
	fsck -y 自动修复
file 判断文件类型

设备挂载
block deivce 块设备，如光盘，标记为b
charset device 字符节设备，如打印机，标记为c

挂载光驱
mount /dev/cdrom /mnt/cdrom
df
cd /mnt/cdrom
ls /mnt/cdrom

卸载光驱
unmount /mnt/cdrom 有时会提示设备忙，如当前就位于/mnt/cdrom下
或
eject 在卸载同时会弹出物理光驱

分区与格式化原理

添加新硬盘后，检测是否正确识别
dmesg | grep sdb

划分分区(fdisk)
fdisk -l /dev/sdb 检测新硬盘信息
fdisk /dev/sdb 分区
	m 帮助
	p 打印分区表
	n 添加新分区
		e 扩展分区
		p 主分区，最多4个主分区，添加分区时不用指定文件系统类型，默认为linux，id为83
	t 改变文件系统类型
	d 删除分区
	w 将分区表写入磁盘保存并退出，不保存则不会生效
	q 不保存设和设置退出

创建文件系统(mkfs)
	mkfs.ext3 /dev/sdb1
尝试挂载(mount)
	mkdir /web
	mount /dev/sdb1 /web
写入配置文件(/etc/fstab)
vi /etc/fstab
---------------------------------------
物理分区名/卷标	挂载点	文件系统	缺省设置	是否检测(1是/0)		检测顺序（0不检测,1优先检测,2后检测）
Label=/		/	ext3		defaults	1			1
/dev/sdb1	/web	ext3		defaults	1			2

e2label /dev/sdb1 apache 增加卷标

磁盘配额

https://zhuanlan.zhihu.com/p/34712557
