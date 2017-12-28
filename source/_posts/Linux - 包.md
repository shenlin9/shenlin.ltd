---
title: linux - 软件包格式
categories:
  - linux
  - rpm
tags:
  - linux
  - rpm
---

Linux 主要提供三种格式的软件包 : rpm格式、二进制格式、源码格式：（tar打包，gz压缩）

<!--more-->

rpm格式：
--------------

    libjpeg-devel-6b-33.x86_64.rpm        #rpm格式很好区分，

    RPM 又分两种：binary rpm 跟 source rpm 。
    前者是编好的 binary ，安装就可用。
    后者是还没编好的 source ，需 rebuild 之后才能安装

    yum其实就是由yum自动判断rpm包的依赖，然后一次性把所有需要安装的N个rpm统一下载安装，其实原理和一个个的安装rpm没有什么本质区别。

二进制包：
--------------

    mysql-3.23.58-pc-linux-i686.tar.gz

    二进制格式的包名字很长，有版本号、适应平台、适应的硬件类型等，格式：mysql-<版本>-<OS>-tar.gz

    二进制包里面包括了已经经过编译，可以马上运行的程序。你只需要下载和解包（安装）它们以后，就马上可以使用。

源码包：
--------------

    php-5.2.14.tar.gz

    而源码格式仅仅就是一个版本号的tar包。

    源码的安装一般由3个步骤组成：配置(configure)、编译(make)、安装(make install)。

    Configure是一个可执行脚本，它有很多选项，在待安装的源码路径下使用命令./configure –help输出详细的选项列表。

    其中--prefix选项是配置安装的路径，如果不配置该选项，安装后
        可执行文件默认放在/usr/local/bin，
        库文件默认放在/usr/local/lib，
        配置文件默认放在/usr/local/etc，
        其它的资源文件放在/usr/local/share
    比较凌乱。

    如果配置--prefix，如：./configure --prefix=/usr/local/test
    可以把所有资源文件放在/usr/local/test的路径中，不会杂乱。

    用了—prefix选项的另一个好处是卸载软件或移植软件。
    当某个安装的软件不再需要时，只须简单的删除该安装目录，就可以把软件卸载得干干净净；移植软件只需拷贝整个目录到另外一个机器即可（相同的操作系统）。

    当然要卸载程序，也可以在原来的make目录下用一次make uninstall，但前提是make文件指定过uninstall。

file命令
---------------- 

Linux下有个命令叫file，因为Linux并不是按照后缀名来判断文件类型的。所以一般在不清楚文件到底是什么类型的时候，就用file这个命令去判断。

#file php-5.2.14.tar.gz
    php-5.2.14.tar.gz: gzip compressed data, was "php-5.2.14.tar", from Unix, last modified: Wed Jul 21 22:32:34 2010, max compression

这个php-5.2.14.tar.gz 明显是个gzip的压缩包，这样的文件一般都是用tar zxvf 命令去解包然后去配置编译安装的，通常情况把这种安装方法叫做源码编译安装。


#file libjpeg-devel-6b-33.x86_64.rpm
    libjpeg-devel-6b-33.x86_64.rpm: RPM v3 bin i386 libjpeg-devel-6b-33

这个libjpeg-devel-6b-33.x86_64.rpm 文件，就是个标准的redhat系列发行版本所用的RPM格式软件包。一般在RHEL、CentOS、SUSE、OracleLinux下都可以安装类似的RPM包。标准的安装方法是rpm -ivh。






