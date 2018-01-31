---
title: linux - Samba 服务器配置
categories:
  - Linux
  - Samba
tags:
  - Linux
  - Samba
---

Samba 服务器配置

<!--more-->

## 历史溯源

早期网络中，文件在多个主机之间传输多使用 FTP 协议，缺点是要更改文件的话就需要在 Client 和 Server 之间来回传输，

且文件在多个主机上存放，有个版本管理的问题。

之后 Unix-Like 系统出现了 NFS （Network File System），可以让 Unix Client 主机直接编辑 Unix Server 主机上的文件，

而 Windows 系统出现了 CIFS （Common Internet File System），可以通过网上邻居共享文件。

而 SAMBA 则是解决了 Windows 和 Unix-Like 系统之间的文件直接共享。

SAMBA 前身称为 SMBServer（Server Message Block），因为 SMB 不能注册为商标，所以改名为 SAMBA，作者是 Tridgwell

## 套件安装

### 使用 RPM来安装

不同的 linux distribution 对于 SAMBA RPM 档案的套件名称不太一样，

例如， Red Hat 9 对于SAMBA 这个服务器总共需要至少三个套件，分别是：

    samba：这个套件主要包含了 SAMBA 的主要 daemon档案 ( smbd 及 nmbd )、 SAMBA 的文件档 ( document )、以及其它与 SAMBA 相关的logrotate 设定文件及开机预设选项档案等；

    samba-common：这个套件则主要提供了 SAMBA 的主要设定档(smb.conf) 、 smb.conf 语法检验的测试程序 ( testparm )等等；

    samba-client：这个套件则提供了当 Linux 做为SAMBA Client 端时，所需要的工具指令，例如挂载 SAMBA 档案格式的执行档 smbmount等等。

不过，在 Mandrake 9.1 当中，则将 samba 这个套件又分为 samba-server与 samba-doc 两个套件，

所以在 MDK 9.1 则有四个套件需要安装：

    samba-server, samba-doc, samba-common, samba-client 。

安装
```bash
[shenlin@t460p ~]$ sudo yum install -y samba

Installed:
  samba.x86_64 0:4.6.2-12.el7_4                                                                                                                                                    
Dependency Installed:
  avahi-libs.x86_64 0:0.6.31-17.el7            cups-libs.x86_64 1:1.6.3-29.el7         libldb.x86_64 0:1.1.29-1.el7                 libtalloc.x86_64 0:2.1.9-1.el7               
  libtdb.x86_64 0:1.3.12-2.el7                 libtevent.x86_64 0:0.9.31-1.el7         libwbclient.x86_64 0:4.6.2-12.el7_4          pytalloc.x86_64 0:2.1.9-1.el7                
  samba-client-libs.x86_64 0:4.6.2-12.el7_4    samba-common.noarch 0:4.6.2-12.el7_4    samba-common-libs.x86_64 0:4.6.2-12.el7_4    samba-common-tools.x86_64 0:4.6.2-12.el7_4   
  samba-libs.x86_64 0:4.6.2-12.el7_4          

Complete!
```

安装检验
```bash
[shenlin@t460p ~]$ rpm -qa|grep samba
samba-common-libs-4.6.2-12.el7_4.x86_64
samba-common-tools-4.6.2-12.el7_4.x86_64
samba-common-4.6.2-12.el7_4.noarch
samba-client-libs-4.6.2-12.el7_4.x86_64
samba-libs-4.6.2-12.el7_4.x86_64
samba-4.6.2-12.el7_4.x86_64
```

### 源码安装

一般来说，因为各个 distribution 提供的 SAMBA 的功能都差不多，所以实在没有必要使用Tarball 来进行额外的安装与设定，不过，如果您还是想要自己建置自己的 SAMBA的话，可以到 SAMBA 的官方网站上下载 samba 的原始程序代码，然后在自己的机器上面编译。

```bash
[root@testroot]sudo wget http://ftp.XX/Unix/Samba/samba-2.2.8a.tar.gz
[root@testroot]sudo cd /usr/local/src
[root@testsrc]sudo  tar -zxvf /root/samba-2.2.8a.tar.gz
这个时候会有一个目录跑出来：/usr/local/src/samba-2.2.8a
[root@testsrc]sudo cd samba-2.2.8a sudo (在这个目录中察看一下README )
[root@testsamba-2.2.8a]sudo  cd source
[root@testsource]sudo  ./configure --prefix=/usr/local/samba \
--with-automount--with-smbmount --with-pam \
--with-mmap--with-quotas --with-libsmbclient

# 开始编译
[root@testsource]sudo make
[root@testsource]sudo make install
```
1. 请先以 ./configure--help 察看一下 configure 的一些相关的参数用法
2. 如果发生任何错误，请不要往下进行make 的动作，因为还是不对的！
3. 万一发生任何错误时，通常是由于一些函式库找不到的缘故，请参考此目录下的 config.log这个档案的内容，里面会记录一些错误的历程。

将刚刚编译完成的可执行 binary 档案安装到 /usr/local/samba 里面 在这个例子当中，未来您在设定 SAMBA 时，必需要到 /usr/local/samba 当中

一般来说，除非 Linux distribution 已经相当的老旧了 (例如 Red Hat6.x 以前的版本)，并且在旧的系统上面正在正常的运作一些服务，而仅想要增加SAMBA 的服务，那就只好使用 Tarball 的方式来安装SAMBA ，否则的话，蛮强烈的建议直接以 RPM 的方法来安装您的SAMBA 服务器软件即可！因为既简单方便，又容易统一设定。Server端的设定由于 SAMBA 几乎一定包含在各个主要的 Linux distribution 当中，并且不同版本之间的功能差异也不是很大，所以，底下的介绍我们都以RPM 安装的 SAMBA 套件来进行说明。当然啦，即使同样是 RPM 的档案，但是在各个Linux distribution 当中， SAMBA 的主要档案放置的目录还是可能会不太一样。不过，因为SAMBA 的设定档档名都是不变的 ( smb.conf )，所以，虽然底下我们是以Red Hat 9 为范例，不过，您依旧可以使用 locate, find, whereis 等指令在不同的distribution 系统下找出 SAMBA 主要的设定档与执行档喔！ ( 这就是为什么我们喜欢教大家使用vi 以及纯文字模式学习 Linux 的原因，因为一法通，万法通啊！)

另外，我一开始的范例当中都是针对没有设定防火墙的情况下所进行设定与测试，如果您的环境里面已经有架设防火墙的话，那么您应该要先了解防火墙的架构，并将SAMBA 需要的 port 给他开放，否则很难测试成功喔！或者直接察看本章节较后面专门谈安全的部分，尤其是iptables 与 /etc/hosts.allow(deny)这部份喔！

套件结构

我们这里以 Red Hat 9 的 SAMBA 套件来介绍他相关的一些设定档与执行档，不过，如果您的distribution 并不是 Red Hat 9 ，那也没有关系，因为都是大同小异的啦！善用locate 这个指令去搜寻喔！

配置文件

在较早期的版本中， SAMBA 的设定档都直接放置在 /etc 底下，后来的版本则将设定档通通放置到/etc/samba 底下去了 ( 有的 distribution 放在 /etc/smb 有的则是 /etc/samba.d，请使用 locate 搜寻！ )。在 /etc/samba 底下的几个重要的设定档有：　/etc/samba/smb.conf：这个就是SAMBA 最主要的设定档了！在较为简单的设定当中，这也是唯一的一个设定档！此外，这个档案本身就含有相当丰富的说明，所以，在设定之前，请使用vi 好好的详细的观看一下这个档案吧！这个设定档主要的设定分为两部份，分别是[global] 这个设定主机功能的项目，以及接下来的每个分享出去的目录的属性设定。

/etc/samba/lmhosts：这个档案的主要目的在对应NetBIOS name 与该主机名称的 IP ，事实上，他有点像是 /etc/hosts 的功能！只不过这个lmhosts 对应的主机名称是 NetBIOS name 喔！不要跟 /etc/hosts 搞混了！由于目前SAMBA 的功能越来越强大，所以通常只要您一启动 SAMBA 时，他就能自己捉到 LAN里面的相关计算机的 NetBIOS name 对应 IP 的信息，因此，这个档案通常可以不用设定了。

/etc/samba/smbpasswd：这个档案预设并不存在。它是SAMBA 预设的使用者密码对应表。当设定的 SAMBA 服务器是较为严密的，需要使用者输入账号与密码后才能登入的状态时，使用者的密码预设就是放置在这里( 当然啰，您可以自行在 smb.conf 里面设定密码放置的地方及密码文件名，不过，我们这里都以预设的状态来说明) 。比较需要注意的是，这个档案因为包含了使用者的密码，所以，当然权限方面要较为注意啦！这个档案的拥有者需要是root ，且权限设定为 600 才行。

执行文件

SAMBA 的执行文件一般来说，做为 SAMBA Server 的执行档有 testparm,smbd, nmbd, smbpasswd，至于做为 SAMBA Client 的执行档主要则是：smbmount,smbclient。　smbd 与nmbd：这两个执行档是那两个主要的 daemons ，每次启动 SAMBA 都会使用到的两个执行档。

testparm：当设定完成了smb.conf 这个主要设定档之后，而想要查看一下 SAMBA 的所有设定参数与 smb.conf的设定项目是否正确时，就需要使用这个 testparm 来查看( test parameters 的简写！)！所以，每次在修改完 smb.conf之后，请务必要使用 testparm 查看看是否有设定错误。

smbpasswd：如果SAMBA 设定的较为严格，需要规定使用者的账号与密码，那么那个密码档案的建立就需要使用smbpasswd 来建置才可以，所以这个指令与建立 SAMBA 的密码有关。　smbclient：当Linux 主机想要藉由『网络上的芳邻』的功能来查看别台计算机所分享出来的目录与装置时，就可以使用smbclient 来查看啦！这个指令也可以使用在 SAMBA 主机上面，用来查看是否设定成功。

smbmount：在Windows 上面可以设定『网络磁盘驱动器』来连接到主机上面，同样，在Linux 上面，可以透过 smbmount 来将远程主机分享的档案与目录挂载到Linux 主机上面。不过，其实也可以直接使用 mount 这个指令来进行同样的功能就是了。[1] 

## 相关目录

这部份需要较为注意的应该算是 SAMBA的『登录档』。因为最近以来，利用『网络上的芳邻』来进行破坏的病毒是越来越多！也有越来越多的搞怪者会以网络上的芳邻的相关漏洞进行入侵的伎俩，所以，了解一下登录档放置的地点，并且加以分析，可以得到不小的监测。　/usr/share/doc/samba：包含SAMBA 的所有相关的技术手册。也就是说，当安装好 SAMBA 之后，系统就已经含有相当丰富而完整的SAMBA 使用手册。
/var/log/samba：SAMBA 预设的登录文件放置目录。如果 SAMBA 老是设定不起来，又或者怀疑被人家以port 137~139 入侵的话，就到这里来观察。
/usr/share/samba/codepages：放置各个语言的支持格式。举例来说，想让SAMBA 支持中文，那么就需要 codepage.950 这个档案的支持。在smb.conf 里面设定即可。

## 应用功能

由上面说明的 SAMBA 发展缘由，可以看出， SAMBA 最初发展的主要目就是要用来沟通Windows 与 Unix Like 这两个不同的作业平台。最大的好处就是不必让同样的一份数据放置在不同的地方，搞到后来都不晓得哪一份资料是最新的！而且也可以透过这样的一个档案系统让Linux 与 Windows 的档案传输变得更为简单！也就是说，可以透过『网络上的芳邻』来进行Linux 与 Windows 档案的传输。那么 SAMBA 可以进行哪些动作呢？
①分享档案与打印机服务；
②提供使用者登入 SAMBA 主机时的身份认证，以提供不同身份者的个别数据；
③进行 Windows 网络上的主机名称解析 (NetBIOS name)
④进行装置的分享 ( 例如 Zip, CDROM... )

## 主要部分

两个守护程序：smbd 和 nmbd（对客户端提供NetBIOS名服务）
配置文件：/etc/smb.conf
使用工具：smbclient,smbstatus,smbmount,smbumount,smbprint,smbprint.sysv,smbrun
samba的启动脚本在/etc/rc.d/init.d/smb
BTW，不要把smb与smp（对称多处理器）搞混了，更不要把NetBIOS名与DNS里的主机名搞混淆了！ samba缺省 是把主机名设置成NetBIOS名，这样通常会超出NetBIOS名的长度限制（16个字符）.

## SMB方法

当登入的使用者尝试连接远端的电脑网络分享，例如 \\server\myshare，Windows 用户端会在向使用者取得任何使用者名称或密码前，自动传送登入使用者的登入资料至
SMB 伺服器，在这步骤，如果认证失败，Windows 会弹出一个视窗，询问使用者名称和密码。
一般来说，SMB 对话以下列次序建立：
"TCP Connection" – 建立 3-way handshake (连线) 至 port 139/tcp 或 445/tcp。
"NetBIOS Session Request" – 使用下列 "Calling Names"：本机的 NetBIOS name
加上第十六个字元 16th character 0×00：伺服器的 NetBIOS name 加上第十六个字元 0×20
"SMB Negotiate Protocol" – 决定使用的协定方言，会是以下其中一项：PC Network Program 1.0
(Core) – 只是分享层级保安模式；Microsoft Networks 1.03 (Core Plus) – 只是分享层级保安模式；Lanman1.0
(LAN Manager 1.0) – 使用 Challenge/Response Authentication；Lanman2.1 (LAN
Manager 2.1) – 使用 Challenge/Response Authentication; NT LM 0.12 (NT LM 0.12)
- 使用 Challenge/Response Authentication
SMB 对话启动，密码会按以下其中一种方法加密 (或不加密)： Null (没有加密)；Cleartext (没有加密)； LM
和 NTLM；NTLM；NTLMv2。接著密码会弄乱并传送给要求对话的电脑 (讽刺地，这步骤会在要求密码前做)。
SMB Tree Connect：连接分享的名称 (例如： \\servername\share)；连接至一种服务类型 (例如： `IPC$ named pipe`)

## 网络邻居

在win95的网络邻居里面看不到Linux box
注意/etc/smb.conf文件里以下几项的设置：
```
guest account = pcguest（不要照着写，添实际的名字，你要去创建一个pcguest帐号）
null password = yes （这一点很重要！）
browseable = yes
public = yes
```
另外把security改为share试试.
仔细读一读"man smb.conf".
再说Win95那个破东西，网上邻居运行一百遍才可能会出来你想要的.
用这个方法试一试：先用smbmount Win95的一个共享目录，用"网络监视器"查看一下，然后再用网上邻居看.
怎么用
不能用man smbmount看看吗? 大致是：
smbmount //win95-name/share-dir /mount-point [-I ip地址或主机名] [-c 本机客户名]
[]表示可选项，本机客户名可以随便取.
Samuel Leo补充道：
标准的smbmount使用格式是
smbmount //server/share -c "mount /mnt -u uid -g gid" （注：好像不对吧）
我编译了一个修改版的smbmount，使用格式为
smbmount //server/share /mnt [passwd] [-Uusername] [-9]
ftp://202.103.190.5/incoming/smbmount.gz (binary)
如果你用redhat，也可以试试最新出的smbwrapper
ftp://202.103.190.5/incoming/smbwrapper.so.gz
设置一下环境变量
```
LD_PRELOAD=/anywhere/
SMBW_USER=username
SMBW_PASSWORD=passsword
SMBW_WORKGROUP=workgroup #optional
SMBW_DEBUG=4 #optional
SMBW_LOGFILE=smbw.log #optional,default to stderr
SMBW_PREFIX=/smb #optional,default to /smb
export PWD SMBW_USER SMBW_PASSWORD SMBW_WORKGROUP
export SMBW_DEBUG SMBW_LOGFILE SMBW_PREFIX
```
然后你就可以"ls /smb"看到同组的所有机器名.
"ls /smb/server"看该机的共享清单.
缺点就是太慢，10.10版对execle,execve，…等指定envp的exec仍有bug
不能下执行/smb下的文件，不能mmap /smb下的文件.

> 先谢谢各位！
> 我的Pwin95现在可以看到linux了，我保证什么也未修改过。
> 现在，我从linux上执行：
> smbclient //sjj2/nes(pwin95机器），可看到文件并显示：
> smb:\>
> smbclient -L sjj2，可看到sjj0(linux）和sjj2(pwin95）。
> 但是我不能从pwin95上访问linux(sjj0），双击总显示：
> 找不到机器名或共享名，请确认输入正确，然后重试。
> 我对smbmount不会用，也找不到能看明白的帮助，因为
> 我不理解mount-point的含义，请指导；linux上的
> smbd和nmbd当然是运行的。
> 再谢各位！请继续帮忙。smb.conf在前面的贴子中。

Win95的网上邻居问题太多，别说跟Linux过不去，就是几台Win95之间连个小网，只要没有NT服务器，他们就经常互相找不到。所以，一定要把samba的WINS服务器功能打开，（wins support = yes），然后把95的WINS服务器指向他。也许还要加入：
name resolv order = wins hosts bcast
这样做的话最好让Linux先于Win95启动起来！
密码问题

>；我在RedHat 5.1里可以共享Win98的服务，在Win98的网上邻居里
>；可以看到LINUX的机子，但提示\\linux\IPC$ 需要口令，输入口令总
>；不正确，不知该如何设置？

此问题好像不只Linux有，NT也有，原因是连接时没有用户名的信息，不要直接点击图标，用磁盘映射：\\linux\username的格式.
Win98使用加密的口令认证，而RedHat的SMB缺省使用明文认证，所以口令总是不正确。
可以在smb.conf中加入
encrypt passwords = yes
并使用smbpasswd 维护用户口令
Win98 上选 开始 -> 注销 ，用 Linux 机器上的用户名和口令登录，然后不用输入口令就可以访问 Linux 的资源了。这和 NT 上是一样的。
或者 Linux 机器上的 /etc/smb.conf 里改成 security = share,
guest account = username (username 改成你机器上的一个用户帐号）。
这样如果 Win98 不是用 Linux 系统上的用户帐号登录的，也可以直接访问 Linux，其权限等于 guest account 指定的用户的权限。Linux作出改动后要重启。
注：完全不必重新启动，可以到/etc/rc.d/init.d下去执行smbd stop，然后再smbd start就可以了（这是在Redhat中）.在Linux下要学会尽量不重启的基本技巧！
发送明文密码
如果你用Win98或打过很多补丁的Win95. 如果Samba不提供口令加密是不能登录的.

1． 执行Win95_PlainPassword.reg允许Win95发送明文口令
运行REGEDIT，添加：
[HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\VxD\VNETSUP]
"EnablePlainTextPassword"=dword:00000001

2． 升级到最新的Samba，打开口令加密开关.
> BTW：哪一版Samba可设加密校验，如何设？
我的 Samba 1.9.18p10 就已经可以了.
在 smb.conf 里面找到

```bash
# You may wish to use password encryption. Please read
# ENCRYPTION.txt,Win95.txt and WinNT.txt in the Samba documentation.
# Do not enable this option unless you have read those documents
encrypt passwords = yes
smb passwd file = /usr/private/smbpasswd
```

这一段就可以了.
相关
smbclient \\NetBIOS-name\share-dir 不对？
这是shell的原因，应该用smbclient \\\\name\\share-dir或者是smbclient '\\name\share-dir'
shell不处理两个单引号之间的内容.如果你熟悉C语言，应该很清楚为什么了！
或者使用smbclient //NetBios-name/share-dir 就不存在这个问题。

> 我在我的Linux机器上设好了smb.conf如下（删去了所有注释），为什么NT不认Samba?

把 security = user
改成：security = server
Samba 1.9.18以前的版本还不完全支持NT的所谓"域",2.0.0版正在开发中，对此有不小的进步.

> 多谢姚飞大侠上回的指点。但我在安装时还是碰到了问题。我选择了shadow，no pam，
> 还选了disk quota，结果出现
> quotas.c:38:sys/quota.h:No such file or director
> 这以后再不能编译了。我把选择该为 shadow no pam,no quota，有编译，出现：
> cc:internal compoler error:program cc1 got fatal signal 6
> make :*** [smbpass.o] Error!
> 我再把shadow,pam quota 等选项选来选去，总是这个错误都不变化了。好像以前编译
> 通过的就不编了只编译后面的。我怎样才能让它重新编译？
> slackware 3.4 kernel 2.0.30
> Thanks!

如果你用的是Slackware 3.4的话，应该是shadow,no pam,no quota
大概从1.9.18p4开始就无法正常编译了，到了smbpass.o必定出这个错.
解决方法有几个：
1. 直接下载编译好的文件
2． 升级GCC到2.8.1，或者 egcs-1.0.2
3． 升级到Slackware 3.5

> 本人单位财务部门需要装一台文件服务器，我安装了Redhat 5.1，用 Samba
> 作为文件服务器，客户端使用的是Win95，现在Win95已可以在网上邻居中找到
> 服务器，我将共享目录映射为F：盘，经过试验，大幅度地拷贝文件都没问题.
> 但是，因为财务软件是dos方式下的，当我执行F:\下的帐务程序时，一次、两次、
> 甚至数次都没问题，但是若干次后每个客户端都出现死机现象。我将Samba
> stop一下，再start就可以了，请问这是怎么一回事？以前用NT做服务器并没有
> 这样的现象。

俺原来用RH 4.2,kernel 2.0.30+ samba 1.9.16p11也有同样的问题update后就ok了，
现在俺用的是kernel 2.0.35 + samba 1.9.18p8

0---------------------------------------------------------------------------------0

NetBios 和 SMB 不可路由，即不能用在互联网上

nmbd 进程：浏览共享即查看linux服务器共享的文件
           计算机名称解析即通过linux服务器的名称而不是IP来访问

`#`注释的是说明信息

`;`注释的是选项

security=指定安全模式
	1、share  	无权限验证
	2、user		缺省的，推荐使用，由linux的Samba服务器做验证
	3、server	第三方主机验证，可以是windows或linux，很少使用
	4、domain	第三方主机验证，第三方必须是windows域控制器，很少使用

hosts allow=允许的主机
hosts denny=限制的主机
一般服务都是禁止优先，但Samba允许优先

网段、IP地址、域名

注意网段192.168.1.0，samba中写为192.168.1.

log file = 日志文件路径

	%m指的是进程名，即smbd和nmbd

服务基本限定就两种：
1、主机限定
2、用户限定


homes段

browseable = no 无权限访问的目录不可见，即只能看到自己的宿主目录
writable = no 只读 yes 可读可写
	也可写为writeable

Linux两种防火墙；
1、Netfilter/Iptables	`#iptables -F`
2、SELinux		`#setsebool -P use_samba_home_dirs on`
				      samba_enable_home_dirs 

			 gesebool -a | grep samba

			或关闭SELinux
			 /etc/selinxu/config SELINUX = disabled

iptables -L

用户必须是系统用户
	apache中可添加虚拟用户，即为服务添加的用户，系统中不存在

设置samba验证密码
	smbpasswd -a 用户名
		-a表示增加密码，没有-a表示修改密码

启动samba

确定smaba进程是否存在

samba查看访问的客户端信息
smbstatus

查看日志信息
cat /var/log/samba


windows建立连接后会记录会话，
net use

删除所有连接
```bash
net use * /delete /y

SSH Secure Shell Client
SSH Secure File Transfer
SecureCRT
```

-------------------------------------------------------

1、chcon -t samba_share_t /software

2、vi /etc/samba/smb.conf

	写在配置文件末尾

	[共享名]
	path = 共享目录，只能是一个共享目录，不能写多个，也不能写文件名
	valid users = 指定访问共享目录的用户，可指定多个，用空格分隔，不写表示所有用户
	writable = 权限

3、重启服务
	/etc/rc.d/init.d/smb restart

用户是否有写权限，需要：
1、samba是否授予写权限
2、用户在linux系统中是否对共享目录有写权限

samba对于写错的选项将忽略，认为没有设置此选项，可以使用下面的命令检测语法错误
testparm

## SELinux

SELinux(Security-Enhanced Linux)

```bash
[shenlin@t460p ~]$ sudo sestatus -v
[sudo] password for shenlin: 
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      28

Process contexts:
Current context:                unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
Init context:                   system_u:system_r:init_t:s0
/usr/sbin/sshd                  system_u:system_r:sshd_t:s0-s0:c0.c1023

File contexts:
Controlling terminal:           unconfined_u:object_r:user_devpts_t:s0
/etc/passwd                     system_u:object_r:passwd_file_t:s0
/etc/shadow                     system_u:object_r:shadow_t:s0
/bin/bash                       system_u:object_r:shell_exec_t:s0
/bin/login                      system_u:object_r:login_exec_t:s0
/bin/sh                         system_u:object_r:bin_t:s0 -> system_u:object_r:shell_exec_t:s0
/sbin/agetty                    system_u:object_r:getty_exec_t:s0
/sbin/init                      system_u:object_r:bin_t:s0 -> system_u:object_r:init_exec_t:s0
/usr/sbin/sshd                  system_u:object_r:sshd_exec_t:s0
```

## 安装缺少的软件包

```bash
[shenlin@t460p ~]$ yum provides /usr/sbin/semanage
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.neusoft.edu.cn
 * extras: mirrors.sohu.com
 * updates: mirrors.sohu.com
policycoreutils-python-2.5-17.1.el7.x86_64 : SELinux policy core python utilities
Repo        : base
Matched from:
Filename    : /usr/sbin/semanage

[shenlin@t460p ~]$ yum provides restorecon
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.neusoft.edu.cn
 * extras: mirrors.sohu.com
 * updates: mirrors.sohu.com
policycoreutils-2.5-17.1.el7.x86_64 : SELinux policy core utilities
Repo        : base
Matched from:
Filename    : /usr/sbin/restorecon

policycoreutils-2.5-17.1.el7.x86_64 : SELinux policy core utilities
Repo        : @anaconda
Matched from:
Filename    : /usr/sbin/restorecon

[shenlin@t460p ~]$ sudo yum install -y policycoreutils
[shenlin@t460p ~]$ sudo yum install -y policycoreutils-python
```

## 创建用于访问共享资源的账户信息

```bash
[shenlin@t460p ~]$ sudo pdbedit -au shenlin
new password:
retype new password:
Unix username:        shenlin
NT username:          
Account Flags:        [U          ]
User SID:             S-1-5-21-1336326196-741977510-2388811566-1000
Primary Group SID:    S-1-5-21-1336326196-741977510-2388811566-513
Full Name:            shenlin
Home Directory:       \\t460p\shenlin
HomeDir Drive:        
Logon Script:         
Profile Path:         \\t460p\shenlin\profile
Domain:               T460P
Account desc:         
Workstations:         
Munged dial:          
Logon time:           0
Logoff time:          Wed, 06 Feb 2036 23:06:39 CST
Kickoff time:         Wed, 06 Feb 2036 23:06:39 CST
Password last set:    Fri, 19 Jan 2018 21:48:33 CST
Password can change:  Fri, 19 Jan 2018 21:48:33 CST
Password must change: never
Last bad password   : 0
Bad password count  : 0
Logon hours         : FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF

[shenlin@t460p ~]$ chown -Rf shenlin:shenlin zaimusic/

[shenlin@t460p ~]$ sudo semanage fcontext -a -t samba_share_t /home/shenlin/zaimusic

[shenlin@t460p ~]$ sudo restorecon -Rv /home/shenlin/zaimusic
restorecon reset /home/shenlin/zaimusic context unconfined_u:object_r:user_home_t:s0->unconfined_u:object_r:samba_share_t:s0

[shenlin@t460p ~]$ getsebool -a | grep samba
samba_create_home_dirs --> off
samba_domain_controller --> off
samba_enable_home_dirs --> off          # <--- 修改此选项
samba_export_all_ro --> off
samba_export_all_rw --> off
samba_load_libgfapi --> off
samba_portmapper --> off
samba_run_unconfined --> off
samba_share_fusefs --> off
samba_share_nfs --> off
sanlock_use_samba --> off
tmpreaper_use_samba --> off
use_samba_home_dirs --> off
virt_use_samba --> off

[shenlin@t460p ~]$ sudo setsebool -P samba_enable_home_dirs on

[shenlin@t460p ~]$ sudo vi /etc/samba/smb.conf

    [global]
            workgroup = WORKGROUP
            security = user

            passdb backend = tdbsam

            printing = cups
            printcap name = cups
            load printers = no
            cups options = raw

    [zaimusic]
            comment = zaimusic
            path = /home/shenlin/zaimusic
            public = no


[shenlin@t460p ~]$ sudo systemctl restart smb

[shenlin@t460p ~]$ systemctl enable smb
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-unit-files ===
Authentication is required to manage system service or unit files.
Authenticating as: shenlin
Password: 
==== AUTHENTICATION COMPLETE ===
Created symlink from /etc/systemd/system/multi-user.target.wants/smb.service to /usr/lib/systemd/system/smb.service.
==== AUTHENTICATING FOR org.freedesktop.systemd1.reload-daemon ===
Authentication is required to reload the systemd state.
Authenticating as: shenlin
Password: 
==== AUTHENTICATION COMPLETE ===
```

下面是设置 iptables 或 firewalld 允许 samba 通过
centos7 的 iptables 默认不开启，改为了 firewalld
且 iptables 和 firewalld 两者只可开启其一，开启
其中一个另一个自动停止运行
下面是分别针对 iptables 和 firewalld 的设置
可以根据实际运行情况，只设置一项即可

设置防火墙
```bash
# 设置防火墙允许 samba 服务通过
[shenlin@t460p ~]$ sudo firewall-cmd --permanent --zone=public --add-service=samba
success

# 查看防火墙允许的服务列表
[shenlin@t460p ~]$ sudo firewall-cmd --permanent --list-service
ssh dhcpv6-client http samba

# 重新载入防火墙规则
[shenlin@t460p ~]$ sudo firewall-cmd --reload
success
```

设置 iptables
```bash
# 清空 iptables ，但这是临时性的，重启后会恢复
[shenlin@t460p ~]$ sudo iptables -F

[shenlin@t460p ~]$ sudo service iptables save
iptables: Saving firewall rules to /etc/sysconfig/iptables:[  OK  ]

# 更合适的方法是在 iptables 中增加规则
# 先查看 iptables 规则
# 这里列出的是在 iptables 启动的情况下加载的规则
# 未启动则没有加载规则，规则列表为空
[shenlin@t460p ~]$ sudo iptables -L --line-number | head
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination
1    ACCEPT     all  --  anywhere             anywhere             ctstate RELATED,ESTABLISHED
2    ACCEPT     all  --  anywhere             anywhere
3    INPUT_direct  all  --  anywhere             anywhere
4    INPUT_ZONES_SOURCE  all  --  anywhere             anywhere
5    INPUT_ZONES  all  --  anywhere             anywhere
6    DROP       all  --  anywhere             anywhere             ctstate INVALID
7    REJECT     all  --  anywhere             anywhere             reject-with icmp-host-prohibited

# 增加规则，注意规则位置，在第 3 条规则上面
[shenlin@t460p ~]$ sudo iptables -I INPUT 3  -p udp -m multiport  --dport 137,138 -j ACCEPT
[shenlin@t460p ~]$ sudo iptables -I INPUT 3 -p tcp -m state --state NEW -m multiport --dport 139,445 -j ACCEPT

# 确认新增加的规则
[shenlin@t460p ~]$ sudo iptables -L --line-number|head -n 12
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination
1    ACCEPT     all  --  anywhere             anywhere             ctstate RELATED,ESTABLISHED
2    ACCEPT     all  --  anywhere             anywhere
3    ACCEPT     tcp  --  anywhere             anywhere             state NEW multiport dports netbios-ssn,microsoft-ds
4    ACCEPT     udp  --  anywhere             anywhere             multiport dports netbios-ns,netbios-dgm
5    INPUT_direct  all  --  anywhere             anywhere
6    INPUT_ZONES_SOURCE  all  --  anywhere             anywhere
7    INPUT_ZONES  all  --  anywhere             anywhere
8    DROP       all  --  anywhere             anywhere             ctstate INVALID
9    REJECT     all  --  anywhere             anywhere             reject-with icmp-host-prohibited

# 保存
[shenlin@t460p ~]$ sudo service iptables save

# 
```
在 RHEL 7 系统中， Samba 服务程序默认
使用的是用户口令认证模式（user）。这种认证模式可以确保仅让有密码且受信任的用户访问
共享资源，而且验证过程也十分简单。不过，只有建立账户信息数据库之后，才能使用用户口
令认证模式。另外， Samba 服务程序的数据库要求账户必须在当前系统中已经存在，否则日
后创建文件时将导致文件的权限属性混乱不堪，由此引发错误。
pdbedit 命令用于管理 SMB 服务程序的账户信息数据库，格式为“pdbedit [选项] 账户”。
在第一次把账户信息写入到数据库时需要使用-a 参数，以后在执行修改密码、删除账户等操
作时就不再需要该参数了。

pdbedit 选项
-a 用户名 建立 Samba 账户
-x 用户名 删除 Samba 账户
-L  列出账户列表
-Lv 列出账户详细信息的列表


创建用于共享资源的文件目录。在创建时，不仅要考虑到文件读写权限的问题，
而且由于/home 目录是系统中普通用户的家目录，因此还需要考虑应用于该目录的 SELinux 安全
上下文所带来的限制。在前面对 Samba 服务程序配置文件中的注释信息进行过滤时，这些过
滤的信息中就有关于 SELinux 安全上下文策略的说明，我们只需按照过滤信息中有关 SELinux
安全上下文策略中的说明中给的值进行修改即可。修改完毕后执行 restorecon 命令，让应用于
目录的新 SELinux 安全上下文立即生效
