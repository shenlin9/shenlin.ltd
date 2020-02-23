---
title: Linux - 磁盘和分区
categories:
  - Linux
tags:
  - Linux
---

Linux 磁盘和分区

<!--more-->

Filesystem                      Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root    3.0G  2.8G  116M  97% /
tmpfs                           250M     0  250M   0% /dev/shm
/dev/sda1                       477M   26M  426M   6% /boot


VBoxManage internalcommands sethduuid "D:\VirtualBox VMs\CenterOS\CenterOS 5.5.vdi"

http://blog.sina.com.cn/s/blog_165c04adb0102wn8x.html

/dev/mapper/VolGroup-lv_root


分区		文件系统		挂载点
/dev/sda1   	ext4
/dev/sda2   	lvm2 pv 	VolGroup

先用virtualbox命令调整虚拟硬盘空间
再用gparted软件分配空间到分区
（上面两步都是修改的virtualbox最大可占用的空间大小，是个逻辑值，真实的磁盘空间不变）
最后再执行下面的命令扩大分区空间
************
df -h

sudo vgdisplay

sudo lvextend -L +1G /dev/mapper/VolGroup-lv_root

sudo resize2fs -p /dev/mapper/VolGroup-lv_root

df -h
************
http://blog.csdn.net/cyjch/article/details/51692684


df -k
显示所有分区的可用空间和已用空间
 找到分区使用率将近100%的分区

sudo  vgdisplay
这个命令显示的是有多少空间还没有被分配，如果显示vgdisplay: command not found 则需要安装lvm2, ubuntu下可以执行sudo apt-get install lvm2来进行安装
(Free  PE / Size       19183 / 74.93 GB         表示你可以添加74.93G)

sudo lvextend -L+20G /dev/vg/home
这个命令会添加20G空间到home分区，这20G空间是从上面的74.93G未分配空间中取得的


有100M的未分配空间


