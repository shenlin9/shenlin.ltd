## 补码

原值取反加 1 得到补码/补数

计算机中的减法运算，其实是使用加法实现的，就是一个原值加上另一个原值的补码

任意一个数和他的补数相加为0

3 - 5 等于 3 + (-5)，从 5 求得 -5 就是使用"取反加1"的方法，5和-5互为补数

http://blog.csdn.net/huangqiang1363/article/details/53449758

## AF PF

AF 表示ADDRESS FAMILY 地址族，PF 表示PROTOCOL FAMILY 协议族，

Winsock2.h中
```
#define AF_INET 0
#define PF_INET AF_INET
```
 
所以在windows中AF_INET与PF_INET完全一样
 
而在Unix/Linux系统中，在不同的版本中这两者有微小差别
对于BSD,是AF,对于POSIX是PF

## Git 迁移后提示证书找不到

```
[2017-08-24 11:46:00] Plugin morhetz/gruvbox
[2017-08-24 11:46:00] $ git clone --recursive "https://github.com/morhetz/gruvbox.git" "C:\Users\ssl\.vim\bundle\gruvbox"
[2017-08-24 11:46:00] > Cloning into 'C:\Users\ssl\.vim\bundle\gruvbox'...
[2017-08-24 11:46:00] > fatal: unable to access 'https://github.com/morhetz/gruvbox.git/': error setting certificate verify locations:
[2017-08-24 11:46:00] >   CAfile: C:/Program Files/Git/mingw32/ssl/certs/ca-bundle.crt
[2017-08-24 11:46:00] >   CApath: none

$ git config --system -l
http.sslcainfo=C:/Program Files/Git/mingw32/ssl/certs/ca-bundle.crt
diff.astextplain.textconv=astextplain

$ git config --system http.sslcainfo C:\cmder\vendor\git-for-windows\mingw32\ssl\certs\ca-bundle.crt

$ git config --system -l
http.sslcainfo=C:\cmder\vendor\git-for-windows\mingw32\ssl\certs\ca-bundle.crt
diff.astextplain.textconv=astextplain
```

## BSD 协议

Berkeley software distribution

BSD 协议被认为是 copycenter，即位于标准的 copyright 和 GPL 的 copyleft 之间。

2-clause BSD-like license 是 BSD 许可协议中最宽松的一种，对开发者使用 BSD 软件只有两个基本的要求：
* 如果再发布的产品中包含有源代码，则源代码中个必须有原来代码中的 BSD 协议
* 如果再发布的产品是二进制形式，则需要在软件的文档或版权声明中包含有原来代码中的 BSD 协议

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


Linux下执行一些命令前加sudo时出现command not found的原因
http://blog.csdn.net/poechant/article/details/7216892
