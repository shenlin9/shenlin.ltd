操作系统主要提供以下基本服务程序：
	
	1、文件系统
		组织和管理数据的方式
	2、设备驱动程序
		管理硬件的方式
	3、用户接口
		用户和系统交互的方式：图形、命令行
	4、系统服务程序
		安装文件系统、启动网络服务等

Unix/Linux发展史

	年代
		1973	Multics项目			Multic庞大复杂
		1973	UNIX
		1971	C语言
		1973	C语言重写了UNIX

	两大分支
		AT&T开发的版本			System V
		加州大学伯克利分校的版本	BSD

	UNIX主要发行版本
		版本		公司		CPU芯片
		AIX			IBM			PowerPC		银行、通信
		HP-UX		HP			PA-RISC		地质勘探、天气
		Solaris		Sun			SPARC		
		Irix		SGI			MIPS
软件的分类
	商业	共享	免费	自由

自由软件
	使用的自由
		可以不受任何限制的使用软件
	研究的自由
		可以获得软件源代码、研究软件运作方式
	散布的自由
		可以自由复制软件及散布给他人
	改良的自由
		可以自行改良软件并散布改良后的版本

Linux应用领域
	网络应用
		基于LAMP的网站论坛以及B/S架构应用
		基于Linux的负载均衡和集群
		基于Linux的防火墙及代理服务器
		基于Linux的网游服务器

		了解服务器信息：www.netcraft.com 点击what's that site running
	嵌入式应用
		生物特征识别系统
		智能卡系统
		智能手机
		路由器
	科学运算
		www.top500.org 全世界运行速度最快的电脑

X Window
	不是某个具体的软件，而是一个标准	

	独立于操作系统
	网络特性
	源代码免费

Unix图形环境
	CDE（）通用桌面环境

Linux技术认证简介
	RHCE	redhat certified engineer
	LPI	linux professional institute
	信息产业部的

安装Linux时必须有的两个目录
	根分区
	swap分区（虚拟内存分区）

远程管理工具
	Putty,SecureCRT	(telnet协议是明文传输的，推荐ssh协议连接，可使用sniffer做监控测试)

文件共享工具
	SSH Secure Shell Client

宿主目录或家目录
	登录用户自己的登录，如/home/zhangsan


/boot	目录，保存系统的引导相关文件，包含内核文件、引导文件grub，大小约为128M
/etc	目录，系统管理员经常需要修改的文件，决定系统行为的配置文件,如apache,dns，备份系统时要备份此目录，修改配置时也需要先备份再修改
/bin	目录，保存用户常用的命令，如文件和目录的操作命令，如vi，ls，这里目录下的命令所有用户都可以使用
/sbin	目录，保存系统维护所使用的命令文件，如分区和格式化的命令，很多命令普通用户无权使用
/lib	目录，系统运行所需要的库文件，如C语言的静态库、动态库文件
/dev	目录，系统设备文件，如/dev/cdrom
/var	目录，保存系统运行时变化的数据，如日志文件、脱机文件
/mnt	目录，用来安装系统文件设备的目录，如光盘/mnt/cdrom
/proc	目录，系统内存和cpu的映射文件，可查看内存的占用，cpu的占用等
/tmp	目录，保存系统运行时产生的临时文件
/usr	目录，保存与用户相关的信息，如一般的安装软件
/home	目录，普通用户的主目录（也称家目录）
/root	目录，系统中超级用户的主目录



系统文件：*.conf	*.rpm
程序与脚本：*.c	*.php
格式文件：.wav	*.jpg	*.html
存档的与压缩文件：*.tar	*.gz

通配符：*	匹配任何数目的任何字符
		？	匹配任何单字符
		[]  匹配任何包含在括号里的单字符

#号提示符说明是超级管理员root
$号提示符说明是普通用户

ls命令：list的缩写，显示目录文件，命令位于bin目录下
		-a	显示所有文件，包含隐藏文件
		-l	长列表显示
		-F	附加文件类型

https://rhn.redhat.com/rhn/software/downloads/SupportedISOs.do


netconfig
service network restart

service iptables stop

iptables -F
iptables -X

service sshd start

ctrl+c 中止一个命令执行

【Linux系统安装设置】

	linux中，使用sd表示scsi硬盘，第一块硬盘表示为sda，第二块表示为sdb，第一块硬盘的第一个分区表示为sda1
	         使用hd表示ide硬盘，第一块硬盘表示为hda，第二块表示为hdb

	必要分区：
		1、根分区
		2、swap分区（不需要挂载点）

	没有单独建立的分区，则使用根目录/的空间，否则是独立的存储空间，但逻辑上仍隶属于根目录

	/root/install.log文件中有完整安装日志

	kickstart文件记录了本次安装时的所作的选项，可实现无人值守安装，位于/root/anaconda-ks.cfg文件中

【ifconfig】
	if表示interface，接口
	eth0		eth表示ethernet，网卡，第一块网卡表示eth0
	lo			表示loopback，回环网卡

【远程登录管理工具】

	putty
	SecureCRT

【退出系统】
	1、输入命令：logout或exit
	2、快捷键：ctrl+D

	[用户名@主机名 宿主目录]命令提示符

【宿主主机和虚拟机通信】
	1、宿主主机已连接到局域网
		vmware网卡设置为网桥bridge
	2、宿主主机没有连接到局域网
		宿主主机中添加一块虚拟网卡：Microsoft Loopback Adapter

【在linux系统中，所有的东西都是文件】

【.和..是两个特殊的目录，？使用ls命令在每个目录下都可以看到？】

【命令执行目录】
	只有root用户才可执行的命令存放目录：
		/sbin
		/usr/sbin

	所有用户都可执行的命令存放目录：
		/bin
		/usr/bin

	bin - binary
	usr - user
	sbin - super binary

【linux的一条默认规则，所有bin目录下的命令都是所有用户可以执行的，无论bin目录位于哪个目录下；所有sbin目录下的命令都是只有root用户才可以执行的，无论sbin目录位于哪个目录下】

【颜色标记，有些儿unix，linux系统ls时是不使用颜色来标记不同的文件类型的，有些儿linux之间的颜色标记也不一致】

【存储数据的最小单位是数据块block，数据块越小，越节省空间，但读取速度越慢，如web站；数据块越大，读取速度越快，但越浪费空间，如视频网站应设置block大一些儿。linux的block默认是512字节，数据块的大小可调整】

【Vim/Vi】

	没有菜单，只有命令

	Vim/Vi三种工作模式：命令模式、插入模式、编辑模式，
	
	vi filename进入命令模式，:wq退出程序
	命令模式下，输入iao进入插入模式，ESC键返回到命令模式
	命令模式下，输入:和编辑命令进入编辑模式，回车则返回命令模式

