【二进制软件包管理】

 一、RPM包管理

    RPM：Read hat Package Manage

    RPM库???

    软件包名称格式

        sudo-1.7.2p1-5.e15.i386.rpm

            sudo    软件名称

            1.7.2p1 版本号

            5.e15   发行号,release

            i386    硬件平台

    安装软件包

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

    卸载软件

        上面的是软件包的名称，而卸载时使用的是软件的名称

        rpm -e sudo

            e表示erase

        rpm -e --nodeps samba

            如果其他软件包对要卸载的软件有依赖关系，卸载时会产生提示信息，使用--nodeps可强行卸载（no dependencies）

    升级软件包

        rpm
            -Uvh [新软件包名]     Upgrade

    校验软件包

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

    查看软件是否按照

        rpm -q samba
                查询samba软件是否已安装

            -qa 查询所有已安装的软件包

                -qa | grep samba
                    查找和samba相关的所有已安装软件包

            -qf [文件路径]  查询文件所属软件包

            -qi [软件名]    查询已安装的软件包的信息
            -qip [软件包名] 查询尚未安装的软件包的信息,p表示package

            -ql [软件名]    查询已安装的软件包在系统中安装了哪些儿文件
            -qlp [软件包名] 查询未安装的软件包将在系统中安装哪些儿文件

            -qd [软件名]    查询已安装的案件包帮助文档的位置
            -qdp [软件包名]    查询未安装的案件包帮助文档的位置

            -qc [软件名]    查询已安装的案件包配置文件的位置
            -qcp [软件包名]    查询未安装的案件包配置文件的位置
	

	软件包文件提取

		解压所有文件到当前目录
		rpm2cpio initscripts-8.45.30-2.el5.centeros.i386.rpm | cpio -idv

		解压指定文件到当前目录
		rpm2cpio initscripts-8.45.30-2.el5.centeros.i386.rpm | cpio -idv ./etc/ininttab


 二、YUM包管理

    引入Yum的好处：

        RPM的依赖关系比较麻烦，yum自动解决软件包的依赖关系
        方便的软件包升级


    YUM包管理

        yum info [软件名]	获取软件信息
        yum install [软件名]
        yum update [软件名]
        yum remove [软件名]	卸载软件

        yum list | grep sudo
        yum list | more       列出yum源上的所有软件包
        yum -C search xxx       在本地缓存中查找软件信息

        yum check-update    检查可更新的软件
        yum check-update    [软件名]

        yum makecache           缓存yum源的软件包信息到本地

        yum -help
        man yum

    更改YUM源

        1，进入yum源配置目录
            cd /etc/yum.repos.d

        2，备份系统自带的yum源
            mv CentOS-Base.repo CentOS-Base.repo.bk

        3，下载163网易的yum源：
            wget http://mirrors.163.com/.help/CentOS6-Base-163.repo

        4，更新yum源后，执行下边命令更新yum配置，使操作立即生效
            yum makecache

【源代码包安装】
    
    源代码包有广泛的适用性
    源代码包很灵活，可按需要修改代码
    很多知名软件的最新版本都是源代码包，而不是二进制包
	源代码包名中不会注明平台类型，如i386或alpha等
	源代码包一般为.tar.gz或.biz2的压缩文件
	源代码包不像二进制包有专门的卸载命令，卸载时将软件卸载的很干净，所以最好自己指定源代码包的安装路径
	源代码包安装的软件卸载方法：关闭进程，删除软件所在目录

    1、解压解包

        #tar -zxvf proftpd-1.3.3d.tar.gz
        #tar -jxvf proftpd-1.3.3d.tar.bz2
        #cd proftpd-1.3.3d

    2、配置，configure是可执行的脚本文件，位于解压的目录里，执行后将收集系统信息，生成makefile文件，为后续的编译make做准备

        #./configure --prefix=/usr/local/proftpd

    3、编译，将源代码编译为二进制文件

        #make

    4、安装，将二进制文件、配置文件复制到指定目录中 

        #make install

	proftpd下载地址：www.proftpd.org
    vsfptd

【源代码包的卸载】

    关闭软件的进程

        kill `pgrep proftpd`;

    将软件的目录删除

        rm -rf /usr/local/proftpd

【脚本安装】

    源代码包安装较复杂，一些儿大型软件采用脚本安装的方式 一般为shell脚本 和 javaScript编写

	应用举例：webmin OpenOffice

	#tar-xzvf webmin-1.530.tar.gz
    #cd webmin-1.530
	#vi README
	#./setup.sh

	www.webmin.com

【脚本安装软件的卸载】

    在解压后的安装包里一般有卸载脚本

【远程管理】

    命令行方式：Putty,secureCRT,XShell

    图形界面：Win C,X Manager

    浏览器：webmin

【APT包管理】

    linux软件包管理分redhat系列 和 debian系列

    搜索软件包：

        apt-cache search

    软件包信息：

        apt-cache show

    安装软件包：

        apt-get install
        apt-get -f install  修复安装
        apt-get reinstall

    删除：

        apt-get remove
        apt-get -purge remove   卸载时不保留配置文件
        apt-get autoremove  卸载时自动解决依赖关系

    更新软件源：

        apt-get update

    更新已安装包：

        apt-get upgrade






