---
title: Linux - 软件包管理 - RPM, YUM, APT, noarch
categories:
  - Linux
tags:
  - Linux
  - RPM
  - YUM
  - APT
  - noarch
---

包括二进制软件包，源码包

<!--more-->

## 二进制软件包管理

### 1. RPM包管理

RPM：Redhat Package Manage

#### RPM 包名格式

sudo-1.7.2p1-5.e15.i386.rpm

sudo    软件名称

1.7.2p1 主版本号.次版本号.修订版本号

5.e15   发行号

i386    硬件平台

#### RPM 包后缀说明

rpm软件包有这么几种后缀：

##### *.386.rpm, *.486.rpm, *.586.rpm, *.686.rpm

这是与CPU的指令集有关.

intel的cpu经过8086,8088,80286,80386,80486,奔腾（586),奔腾二代（686)，奔腾 三代（686)每次换代，都增加了一些新的指令集,但都向后兼容。

软件包为了能发挥好cpu的全部性能，就加入cpu相对应能执行的指令。因此就产生了各种不同的软件包。所以，i686的软件包能在奔腾二代以上的cpu上执行，但基本不能在此之先的cpu如486上执行。而i386的软件包既可在i386 的电脑上执行，也可在后面所有的cpu上执行（如奔三，但不能发挥cpu的最佳性能。）

##### *.athlon.rpm

这种装在AMD系统上更能发挥好AMD cpu的性能。

##### *.noarch.rpm

noarch，no architecture 
表明与处理器无关，这类包可以在各个不同的cpu上使用，
可以安装在任何架构，可能为x86，SPARC或字母为基础的系统。

##### *.src.rpm

这类软件包是包含了源代码的 rpm 包， 在安装时需要进行编译。在编译时会根据cpu的类型来产生相应后缀的软件包.


以安装oracle-validated-1.1.0-21.el5.src.rpm为例
```bash
#先安装 rpm build 工具
yum install rpm* rpm-build rpmdev*

rpm -i oracle-validated-1.1.0-21.el5.src.rpm
cd /usr/src/redhat/SPECS
rpmbuild -bb oracle-validated.spec
cd /usr/src/redhat/RPMS/x86_64
rpm -Uvh oracle-validated-1.1.0-21.el5.x86_64.rpm
```

#### RPM 相关命令

##### 安装软件包

    rpm -ivh sudo-1.7.2p1-5.e15.i386.rpm
    
    rpm -i install
        -v 详细信息
        -h 进度
        --excludedocs 不安装软件包中的文档文件
        --prefix=PATH 将软件包安装到由PATH指定的路径下，但大多数RPM安装包已包含了安装目录信息，不允许改变安装目录
        --test	  只对安装进行测试，并不实际安装
        --replacepkgs 软件包已安装，覆盖安装
        --replacefiles 要安装的软件包中有一个文件已在安装其他软件包时被安装了，会提示conflicts with file xxx，使用此选项替换文件
        --nodeps 软件包依赖于其他软件包，忽略依赖关系强制安装（但很可能因缺少依赖关系而导致可以安装但无法运行）

##### 卸载软件

上面的是软件包的名称，而卸载时使用的是软件的名称

    rpm -e sudo

        e表示erase

    rpm -e --nodeps samba

        如果其他软件包对要卸载的软件有依赖关系，卸载时会产生提示信息，使用--nodeps可强行卸载（no dependencies）

##### 升级软件包

    rpm
        -Uvh [新软件包名]     Upgrade

##### 校验软件包

    rpm -V [软件名]     V表示Verify

    软件包安装的每一个文件都没有做过更改，则不显示任何信息，否则：

    #rpm -V sudo

    #S.5....T   c /etc/sudoers

        5 文件的MD5校验值
        S 文件大小
        L 链接文件
        T 创建或更新时间
        D 设备文件
        U 文件的用户
        G 文件的用户组
        M 文件的权限

    md5sum可以为文件生成MD5值

        md5sum /etc/services

##### 查询软件包

    rpm -q samba
            查询samba软件是否已安装

        -qa
            查询所有已安装的软件包

        -qa | grep samba
            查找和samba相关的所有已安装软件包
            
        -------------------

        -qf [文件]
            查询文件所属软件包

        -qi [软件名]
            查询已安装的软件包的信息
            
        -qip [软件包名]
            查询尚未安装的软件包的信息,p表示package

        -ql [软件名]
            查询已安装的软件包在系统中安装了哪些儿文件
            
        -qlp [软件包名]
            查询未安装的软件包将在系统中安装哪些儿文件

        -------------------
        
        -qd [软件名]
            查询已安装的案件包帮助文档的位置
            
        -qdp [软件包名]
            查询未安装的案件包帮助文档的位置

        -------------------
        
        -qc [软件名]
            查询已安装的案件包配置文件的位置
            
        -qcp [软件包名]
            查询未安装的案件包配置文件的位置

##### 提取软件包文件

	解压所有文件到当前目录
	rpm2cpio initscripts-8.45.30-2.el5.centeros.i386.rpm | cpio -idv

	解压指定文件到当前目录
	rpm2cpio initscripts-8.45.30-2.el5.centeros.i386.rpm | cpio -idv ./etc/ininttab

##### 完整性验证

    RPM 内置完整性验证机制
    ????

### 2. 软件包管理器 YUM

YUM : Yellowdog Updater,Modified

Linux 有个发行版本叫 Yellow Dog Linux，这个发行版的包管理器是 Yellowdog UPdater，简称 YUP，

后来 YUP 被 Modified 后成为 RedHat 的包管理器，即YUM。

YUM 现在是 Fedora，RedHat 和 SUSE 中基于 rpm 的软件包管理器

> yellow dog 在英语口语中的含义并不是“黄色的狗”，而是卑鄙小人的意思，Yellow 在英语中多指“胆怯、懦弱” 
> 但 yellow dog linux 是黄狗 linux
> lucky dog 在英语中表示 幸运儿

yum是软件的仓库，它可以是http或ftp站点，也可以是本地软件池，
但必须包含rpm的header，header包括了rpm包的各种信息，包括描述，功能，提供的文件，依赖性等.
正是收集了这些 header并加以分析，才能自动化地完成余下的任务

#### 引入 Yum 的好处

    RPM的依赖关系比较麻烦，yum自动解决软件包的依赖关系，
    能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，
    并且一次安装所有依赖的软体包，无须繁琐地一次次下载、安装。
    可以使系统管理人员自动化管理RPM软件包，

#### 配置文件

/etc/yum.conf

#### update 和 upgrade

经 man yum 确认，

`yum upgrade` 等价于 `yum update --obsolets`，表示更新时会更新过时的 RPM 包，

但因为默认情况下 yum 的配置文件 /etc/yum.conf 中 obsolets 的值就是 1，

所以这两个命令`yum update`和`yum upgrade`就是完全一样的

???
可能 apt-get 的 update ，upgrade 可能不同，如 update 是更新软件包信息？而 upgrade 是升级软件包本身，需确认

要求在install操作之前执行update和upgrade，实际上是确保本地软件列表信息和已安装软件均为最新的过程。这样做可以最大限度地确保新安装的软件包正常工作。

一般来说，update和upgrade不需要每次安装软件之前都运行，安装新软件的话一天左右运行一次即可，不安装软件的时候隔十天半个月运行一下来更新软件包，

服务器系统如果没有安全性更新就别乱更新了，稳定最重要。

       update 
       
            If run without any packages, update will update every currently installed package.  If one or more packages or package globs are specified, Yum will only update
              the listed packages.  While updating packages, yum will ensure that all dependencies are satisfied. (See Specifying package names for more information)  If  the
              packages  or  globs  specified match to packages which are not currently installed then update will not install them. update operates on groups, files, provides
              and filelists just like the "install" command.

              If the main obsoletes configure option is true (default) or the --obsoletes flag is present yum will include package obsoletes in its calculations - this  makes
              it better for distro-version changes, for example: upgrading from somelinux 8.0 to somelinux 9.

              Note  that  "update"  works on installed packages first, and only if there are no matches does it look for available packages. The difference is most noticeable
              when you do "update foo-1-2" which will act exactly as "update foo" if foo-1-2 is installed. You can use the "update-to" if you'd prefer that nothing happen  in
              the above case.

       upgrade
              Is the same as the update command with the --obsoletes flag set. See update for more details.

        check-update
              Implemented so you could know if your machine had any updates that needed to be applied without running it interactively. Returns exit value of 100 if there are
              packages available for an update. Also returns a list of the packages to be updated in list format. Returns 0 if no packages are available for update. Returns 1
              if an error occurred.  Running in verbose mode also shows obsoletes.

       repolist
              Produces a list of configured repositories. The default is to list all enabled repositories. If you pass -v, for verbose mode, or use repoinfo then more  infor‐
              mation is listed. If the first argument is ´enabled´, ´disabled´ or ´all´ then the command will list those types of repos.

              You  can  pass  repo  id  or  name arguments, or wildcards which to match against both of those. However if the id or name matches exactly then the repo will be
              listed even if you are listing enabled repos. and it is disabled.

              In non-verbose mode the first column will start with a ´*´ if the repo. has metalink data and the latest metadata is not local and will start with a ´!´ if  the
              repo. has metadata that is expired. For non-verbose mode the last column will also display the number of packages in the repo. and (if there are any user speci‐
              fied excludes) the number of packages excluded.

              One last special feature of repolist, is that if you are in non-verbose mode then yum will ignore any repo errors and output the information  it  can  get  (Eg.
              "yum clean all; yum -C repolist" will output something, although the package counts/etc. will be zeroed out).

       repoinfo

              This command works exactly like repolist -v.

#### YUM 命令

格式：yum [选项] 子命令

选项

    -y：对所有的提问都回答“yes”；
    -C：完全从缓存中运行，而不去下载或者更新任何头文件。

    -c：指定配置文件；
    -q：安静模式；
    -v：详细模式；
    -R：设置yum处理一个命令的最大等待时间；

    --security: 用于 update 时只包含修复安全问题的包

子命令

    yum info [软件名]           获取软件信息
    yum groupinfo group1        显示程序组group1信息

    yum install [软件名]        
    yum groupinsall group1      安装程序组group1
    yum localinstall            安装本地的rpm软件包；
    yum localupdate             显示本地rpm软件包进行更新；

    yum remove [软件名]	        卸载软件
    yum groupremove group1             #删除程序组group1
    yum erase [软件名]	        卸载软件

    yum list
    yum list | more             列出yum源上的所有软件包
    yum list installed          列出已安装的软件包
    yum list | grep sudo
    yum list package1           显示指定程序包安装情况package1
    yum search xxx              根据关键字xxx查找安装包
    yum -C search xxx           在本地缓存中查找软件信息

    yum provides "*/nmcli"      查找命令所属软件包
    yum deplist package1        查看程序package1依赖情况
    yum resolvedep              显示rpm软件包的依赖关系；

    yum update                  全部更新
    yum update [软件名]         更新指定软件
    yum upgrade [软件名]        同 update
    yum check-update            检查可更新的软件
    yum check-update [软件名]   检查指定软件是否可更新
    yum --security check-update 列出所有有安全更新的包
                                To list all updates that are security relevant, and get a return code on whether there are security updates use:
    yum --security update       只更新有安全更新的包
                                To upgrade packages that have security errata (upgrades to the latest available package) use:

    yum makecache           缓存yum源的软件包信息到本地
                            配合yum -C search xxx使用，不用上网检索就能查找软件信息
                            执行完 yum makecache之后，你可以用
                                yum search subversion
                            和
                                yum -C search subversion
                            试下看看二者速度差别

    yum 会把下载的软件包和header存储在cache中，而不会自动删除。???配置文件中默认好像是不保存缓存keepcache=0
    yum clean packages       清除缓存目录下的软件包
    yum clean headers        清除缓存目录下的 headers
    yum clean oldheaders     清除缓存目录下旧的 headers
    yum clean all            清除软件包和headers

    yum -h
    yum --help
    man yum

#### Yum 源相关命令

    yum repolist
    yum repolist enabled
    yum repolist disabled
    yum repolist all


    yum-config-manager --enable epel
    ??? 区别
    yum repolist --enablerepo=epel

    yum repolist all|grep epel
        * epel: mirror2.totbb.net
        epel/x86_64                        Extra Packages for Enterprise enabled: 12,400

    yum repoinfo epel
        Loaded plugins: fastestmirror
        Loading mirror speeds from cached hostfile
        Repo-id      : epel/x86_64
        Repo-name    : Extra Packages for Enterprise Linux 7 - x86_64
        Repo-status  : disabled
        Repo-metalink: https://mirrors.fedoraproject.org/metalink?repo=epel-7&arch=x86_64
        Repo-expire  : 21,600 second(s) (last: Wed Jan 31 15:07:57 2018)
          Filter     : read-only:present
        Repo-filename: /etc/yum.repos.d/epel.repo

#### 更改 YUM 源

1，进入yum源配置目录

    cd /etc/yum.repos.d

2，备份系统自带的yum源

    mv CentOS-Base.repo CentOS-Base.repo.bk

3，下载网易的yum源或阿里的yum源：

    wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo
    或
    curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

4，更新yum源后，执行下边命令更新yum配置，使操作立即生效

    yum clean all
    yum makecache


网易开源镜像站：http://mirrors.163.com/
阿里开源镜像站：http://mirrors.aliyun.com/

### 3. APT包管理

linux软件包管理分redhat系列 和 debian系列

#### 搜索软件包

    apt-cache search

#### 软件包信息

    apt-cache show

#### 安装软件包

    apt-get install
    apt-get -f install  修复安装
    apt-get reinstall

#### 删除软件包

    apt-get remove
    apt-get -purge remove   卸载时不保留配置文件
    apt-get autoremove  卸载时自动解决依赖关系

#### 更新软件源

    apt-get update

#### 更新已安装包

    apt-get upgrade
        
## 源代码包管理
    
源代码包有广泛的适用性
源代码包很灵活，可按需要修改代码
很多知名软件的最新版本都是源代码包，而不是二进制包
源代码包名中不会注明平台类型，如i386或alpha等
源代码包一般为.tar.gz或.biz2的压缩文件
源代码包不像二进制包有专门的卸载命令，卸载时将软件卸载的很干净，所以最好自己指定源代码包的安装路径
源代码包安装的软件卸载方法：关闭进程，删除软件所在目录

### 手工安装

#### 1. 解压包

```
$ tar -zxvf proftpd-1.3.3d.tar.gz
$ tar -jxvf proftpd-1.3.3d.tar.bz2
$ cd proftpd-1.3.3d
```

#### 2. 配置
    
configure是可执行的脚本文件，位于解压的目录里，执行后将收集系统信息，生成makefile文件，为后续的编译make做准备
```
$ ./configure --prefix=/usr/local/proftpd
```
#### 3. 编译

将源代码编译为二进制文件
```
$ make
```
#### 4. 安装

将二进制文件、配置文件复制到指定目录中 
```
$ sudo make install
```

#### 5. 卸载

关闭软件的进程
```
kill `pgrep proftpd`;
```

将软件的目录删除
```
rm -rf /usr/local/proftpd
```

### 脚本安装

#### 安装

源代码包安装较复杂，一些儿大型软件采用脚本安装的方式 一般为shell脚本 和 javaScript编写，应用举例：webmin OpenOffice
```
$ tar-xzvf webmin-1.530.tar.gz
$ cd webmin-1.530
$ vi README
$ ./setup.sh
```

#### 卸载

    在解压后的安装包里一般有卸载脚本

## 源码包与RPM包的区别

1.安装之前的区别：概念上的区别
源码包是开源的，比RPM包安装更自由，但是它安装更慢，更容易报错；
RPM包是经过编译的，不能看到源代码，但是它安装更快，报错更容易解决，只有依赖性问题。

2.安装之后的区别：安装位置不同
源码包安装在人为手工指定的位置中，一般是/usr/local/软件名/
RPM包不需要指定安装位置，它会安装到系统默认位置：

    /etc/ 配置文件安装目录
    /usr/bin/ 可执行的命令安装目录
    /usr/lib/ 程序所使用的函数库保存位置
    /usr/share/doc/ 基本的软件使用手册保存位置
    /usr/share/man/ 帮助文件保存位置

3.安装位置不同带来的影响

RPM包安装的服务可以使用系统服务管理命令（service）来管理，
例如RPM包安装的apache的启动方法是：

    /etc/rc.d/init.d/httpd start
    service httpd start

源码包安装的服务则不能被服务管理命令管理，因为没有安装到默认路径中。
所以只能用路径进行服务的管理，如：

    /usr/local/apache2/bin/apachectl start
