---
title: linux - Filesystem Hierarchy Standard 文件系统层次化标准
categories:
  - Linux
tags:
  - Linux
  - FHS
---

Filesystem Hierarchy Standard 文件系统层次化标准

https://zh.wikipedia.org/wiki/%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F%E5%B1%82%E6%AC%A1%E7%BB%93%E6%9E%84%E6%A0%87%E5%87%86#cite_ref-11
http://www.pathname.com/fhs/pub/fhs-2.3.html
http://www.pathname.com/fhs/

<!--more-->

因为Linux的开发人员实在太多了，如果每个人都使用自己的目录配置方法，那么将可能会带来很多管理问题。你能想象，你进入一个企业之后，所接触到的Linux目录配置方法竟然跟你以前学的完全不同吗？所以，后来就有所谓的文件系统层次标准(Filesystem Hierarchy Standard，FHS)出台。

多数Linux版本采用这种文件组织形式，类似于Windows操作系统中c盘的文件目录，FHS采用树形结构组织文件。

FHS定义了系统中每个区域的用途、所需要的最小构成的文件和目录，同时还给出了例外处理与矛盾处理。

在FHS中，所有的文件和目录都出现在根目录"/"下，即使他们存储在不同的物理设备中。
## 规范

FHS定义了两层规范

第一层是 `/` 下面的各个目录应该要放什么文件数据，例如 `/etc` 应该要放置设置文件，`/bin` 与 `/sbin` 则应该要放置可执行文件等等。

第二层则是针对/usr及/var这两个目录的子目录来定义。例如/var/log放置系统登录文件、/usr/share放置共享数据等等。

## 特点

由于FHS仅是定义出最上层(/)及子层(/usr, /var)的目录内容应该要放置的文件数据，因此，在其他子目录层级内，就可以随开发人员自行配置了。举例来说，FC4的网络设置数据放在/etc/sysconfig/network-script/目录下，但SuSE Server 9则是将网络放在/etc/sysconfig/network/目录下，目录名称是不同的。

另外，在Linux中，所有的文件与目录都由根目录/ 开始。那是所有目录与文件的源头。然后再一个一个分支下来，有点像树状。因此，我们也称这种目录配置方式为：“目录树(directory tree)”。

这个目录树主要特性有：
* 目录树的起始点为根目录(/, root)。
* 每一个目录不仅能使用本地端分区的文件系统，也可以使用网络上的文件系统。举例来说，可以利用网络文件系统(Network File System，NFS)服务器载入某特定目录等。
* 每一个文件在此目录树中的文件名(包含完整路径)都是独一无二的。

此外，根据文件名写法的不同，也可将路径(path)定义为绝对路径(absolute)与相对路径(relative)。
绝对路径为：由根目录(/)开始写起的文件名或目录名称，例如/home/dmtsai/.bashrc；
相对路径为相对于当前路径的文件名写法。例如./home/dmtsai或../../home/dmtsai/等等。反正开头不是/ 就属于相对路径的写法。必须要了解，相对路径是以“当前所在路径的相对位置”来表示的。举例来说，当前在/home目录下，如果想要进入/var/log目录时，怎么写呢？
```bash
cd /var/log(absolute)
cd ../var/log(relative)
```
因为在/home中，所以要回到上一层(../)之后，才能继续向/var移动。

特别注意这两个特殊的目录：
.：表示当前目录，也可以使用./来表示。
..：表示上一层目录，也可以../来表示。
.与..的目录概念很重要，你常常会看到cd ..或 ./command之类的命令方式，就是表示上一层与当前所在目录的工作状态。

此外，针对“文件名”与“完整文件名(由/ 开始写起的文件名)”的字符限制大小为：单一文件或目录的最大容许文件名为255个字符。包含完整路径名称及目录(/)的完整文件名为4096个字符。

我们知道，/var/log/下面有个文件名为message，这个message文件的最大文件名可达255个字符。var与log这两个上层目录最长也分别可达255个字符。但总的来说， /var/log/messages这样完整的文件名最长则可达4096个字符。

提示:root在Linux里面的意义很多。如果从“账号”的角度来看，root指“系统管理员”身份，如果以“目录”的角度来看，root指的是根目录，就是/ 。要特别注意。

## 目录

### etc

初期：早期UNIX中，贝尔实验室的解释是：etcetra directory 。 etc. 就是Et cetra。表示其他、等等什么的，英语里能常常看都这个缩写的。是用来放其他不能归类到其他目录中的内容。
后来FHS规定用来放配置文件，就解释为："Editable Text Configuration" 或者 "Extended Tool Chest"。

### opt

Optional application software packages

http://baike.baidu.com/item/FHS
http://www.cnblogs.com/lifeinsmile/p/4280223.html
http://www.cnblogs.com/happyframework/p/4480228.html

    /           第一层次结构 的根、 整个文件系统层次结构的根目录。
    /bin/       需要在单用户模式可用的必要命令（可执行文件）；面向所有用户，例如： cat、 ls、 cp。
    /boot/      引导程序文件，例如： kernel、initrd；时常是一个单独的分区[6]
    /dev/       必要设备, 例如：, /dev/null.

    /etc/       特定主机，系统范围内的配置文件。
                关于这个名称目前有争议。在贝尔实验室关于UNIX实现文档的早期版本中，/etc 被称为etcetera， [7] 这是由于过去此目录中存放所有不属于别处的所有东西（然而，FHS限制/etc存放静态配置文件，不能包含二进制文件）。 [8] 自从早期文档出版以来，目录名称已被以各种方式重新称呼。最近的解释包括反向缩略语如："可编辑的文本配置"（英文 "Editable Text Configuration"）或"扩展工具箱"（英文 "Extended Tool Chest")。 [9]
    /etc/opt/   /opt/的配置文件
    /etc/X11/   X Window系统(版本11)的配置文件
    /etc/sgml/  SGML的配置文件
    /etc/xml/   XML的配置文件

    /home/ 	    用户的家目录，包含保存的文件、个人设置等，一般为单独的分区。
    /lib/ 	    /bin/ 和 /sbin/中二进制文件必要的库文件。
    /media/ 	可移除媒体(如CD-ROM)的挂载点 (在FHS-2.3中出现)。
    /mnt/ 	    临时挂载的文件系统。
    /opt/ 	    可选应用软件 包。[10]
    /proc/ 	    虚拟文件系统，将内核与进程状态归档为文本文件。例如：uptime、 network。在Linux中，对应Procfs格式挂载。
    /root/ 	    超级用户的家目录
    /sbin/ 	    必要的系统二进制文件，例如： init、 ip、 mount。
    /srv/ 	    站点的具体数据，由系统提供。
    /tmp/ 	    临时文件(参见 /var/tmp)，在系统重启时目录中文件不会被保留。

    /usr/ 	        用于存储只读用户数据的第二层次； 包含绝大多数的(多)用户工具和应用程序。[11]
    /usr/bin/       非必要可执行文件 (在单用户模式中不需要)；面向所有用户。
    /usr/include/   标准包含文件。
    /usr/lib/       /usr/bin/和/usr/sbin/中二进制文件的库。
    /usr/sbin/      非必要的系统二进制文件，例如：大量网络服务的守护进程。
    /usr/share/     体系结构无关（共享）数据。
    /usr/src/       源代码,例如:内核源代码及其头文件。
    /usr/X11R6/     X Window系统 版本 11, Release 6.
    /usr/local/     本地数据的第三层次， 具体到本台主机。通常而言有进一步的子目录， 例如：bin/、lib/、share/.

    /var/ 	        变量文件——在正常运行的系统中其内容不断变化的文件，如日志，脱机文件和临时电子邮件文件。有时是一个单独的分区。
    /var/cache/     应用程序缓存数据。这些数据是在本地生成的一个耗时的I/O或计算结果。应用程序必须能够再生或恢复数据。缓存的文件可以被删除而不导致数据丢失。
    /var/lib/       状态信息。 由程序在运行时维护的持久性数据。 例如：数据库、包装的系统元数据等。
    /var/lock/      锁文件，一类跟踪当前使用中资源的文件。 
    /var/log/       日志文件，包含大量日志文件。
    /var/mail/      用户的电子邮箱。 
    /var/run/       自最后一次启动以来运行中的系统的信息，例如：当前登录的用户和运行中的守护进程。现已经被/run代替[13]。 
    /var/spool/     等待处理的任务的脱机文件，例如：打印队列和未读的邮件。 
    /var/spool/mail/用户的邮箱(不鼓励的存储位置) 
    /var/tmp/       在系统重启过程中可以保留的临时文件。

    /run/ 	        代替/var/run目录。

## 目录

/home               普通用户的家目录
/bin                普通用户使用的命令
/usr/bin            普通用户使用的命令，系统自带的软件包可执行文件的安装目录
/usr/local/bin      普通用户使用的命令，用户通过源码编译的软件包的可执行文件目录

/root               超级用户 root 的家目录
/sbin               超级用户使用的命令
/usr/sbin           超级用户使用的命令
/usr/local/sbin     超级用户使用的命令

/boot               linux 内核和系统引导程序（GRUB,LILO）所在的目录
/dev                设备文件存储目录，如磁盘、声卡……
/lib                库文件
/proc               进程信息和内核信息，里面的文件都是伪文件，实际是内存的映射，定义参看 /etc/fstab
/opt                软件安装目录
/mnt                存储设备的挂载目录

/etc                系统配置文件和一些儿服务器的配置文件
/etc/init.d         以 System V 模式启动的脚本
/etc/xinit.d        以 xinetd 模式启动的脚本
/etc/rc.d           以 BSD 方式启动的脚本
/etc/X11            X-Windows 相关的配置文件

/usr                程序目录
/usr/src            内核源码的存放目录，源码软件包
/usr/local          用户通过源码包自编译软件的安装目录
/usr/share          系统共用的资源
/usr/share/fonts    字体目录
/usr/share/man      帮助目录
/usr/share/doc      帮助目录

/var                经常变换的数据
/var/adm            软件包安装信息、日志、管理信息
/var/spool          打印机、邮件、代理服务器等假脱机目录
/var/log            系统日志目录
/var/www            Apache 站点目录
/var/lib            库文件，如 MySql 的库文件


/log                日志文件
/tmp                临时文件目录
/lost+found         系统意外关机或崩溃后产生的碎片文件放到此目录，系统启动时会检查此目录的碎片文件并尝试修复摔坏的文件系统


## 整理过的

/usr                Unix Shared Resources
                    最初的含义就是 user，是 Unix 系统是用来存放用户家目录的，即相当于现在的 /home 目录，
                    但现在用来存放共享的可执行文件、库
