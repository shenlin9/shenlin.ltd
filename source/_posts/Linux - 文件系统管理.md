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
/usr/sbin /sbin 存放只有root可以执行的命令
/home 用户缺省宿主目录
/proc 虚拟文件系统，存放当前内存镜像（缺省是保存在内存镜像中的）
/dev 存放设备文件
/lib 存放系统程序运行所需的共享库
/lost+found 存放一些儿系统出错的检查结果
/tmp 存放临时文件，年作为？
/etc 存放系统配置文件，备份系统时重要备份目录
/var 包含经常发生变动的文件，如邮件、日志文件、计划任务等
/usr 存放所有命令、库和手册页 类似于c:\windows
/mnt 临时文件系统(光盘、软盘、u盘)的安装点
/boot 内核文件及自举程序文件保存位置，备份系统时重要备份目录
/usr/local 第三方程序安装目录，类似于c：\programe files

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
