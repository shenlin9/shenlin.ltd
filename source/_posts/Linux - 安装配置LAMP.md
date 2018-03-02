---
title: Linux - 源码编译、安装、配置 LAMP
categories:
  - Linux
tags:
  - LAMP
---

源码编译、安装、配置 LAMP

<!--more-->

## 安装 PHP

解压

    tar -zxvf php-5.5.11.tar.gz

配置安装变量

    ./configure --prefix=/usr/local/servers/php 

编译

    make

安装

    make install

创建软链接

    ln -s /usr/local/servers/php/bin/php /usr/local/bin/php

## 安装 Apache

Extract

    $ gzip -d httpd-NN.tar.gz
    $ tar xvf httpd-NN.tar
    $ cd httpd-NN

Configure

    $ ./configure --prefix=PREFIX

Compile

    $ make

Install

    $ make install

Customize

    $ vi PREFIX/conf/httpd.conf

Test

    $ PREFIX/bin/apachectl -k start


### Problems

Apache Portable Runtime (APR)
Apache Portable Runtime Utility Library(APR-util)
PCRE - Perl Compatible Regular Expressions

mkdir -p


## Apache

```bash
$bunzip2 -k httpd-2.4.25.tar.bz2

$tar -xf httpd-2.4.25.tar

$configure --prefix=/usr/local/apache2.4.25
    -bash: configure: command not found

$./configure --prefix=/usr/local/apache2.4.25
    checking for chosen layout... Apache
    checking for working mkdir -p... yes
    checking for grep that handles long lines and -e... /bin/grep
    checking for egrep... /bin/grep -E
    checking build system type... i686-pc-linux-gnu
    checking host system type... i686-pc-linux-gnu
    checking target system type... i686-pc-linux-gnu
    configure: 
    configure: Configuring Apache Portable Runtime library...
    configure: 
    checking for APR... no
    configure: error: APR not found.  Please read the documentation.

$wget -P ~ http://mirrors.hust.edu.cn/apache//apr/apr-1.5.2.tar.gz

$bunzip2 -k ~/apr-1.5.2.tar.gz 
    bunzip2: Cant guess original name for /home/ssy/apr-1.5.2.tar.gz -- using /home/ssy/apr-1.5.2.tar.gz.out
    bunzip2: /home/ssy/apr-1.5.2.tar.gz is not a bzip2 file.

$./configure --prefix=/usr/local/apr1.5.2
    checking build system type... i686-pc-linux-gnu
    checking host system type... i686-pc-linux-gnu
    checking target system type... i686-pc-linux-gnu
    Configuring APR library
    Platform: i686-pc-linux-gnu
    checking for working mkdir -p... yes
    APR Version: 1.5.2
    checking for chosen layout... apr
    checking for gcc... no
    checking for cc... no
    checking for cl.exe... no
    configure: error: in `/home/ssy/apr-1.5.2':
    configure: error: no acceptable C compiler found in $PATH
    See `config.log' for more details

$rpm -qa|grep gcc
    libgcc-4.2
$rpm -q gcc
    package gcc is not installed
$rpm -q libgcc
    libgcc-4.4.7-18.el6.i686
$yum -y install gcc
    解决依赖关系
    --> 执行事务检查
    ---> Package gcc.i686 0:4.4.7-18.el6 will be 安装
    --> 处理依赖关系 libgomp = 4.4.7-18.el6，它被软件包 gcc-4.4.7-18.el6.i686 需要
    --> 处理依赖关系 cpp = 4.4.7-18.el6，它被软件包 gcc-4.4.7-18.el6.i686 需要
    --> 处理依赖关系 glibc-devel >= 2.2.90-12，它被软件包 gcc-4.4.7-18.el6.i686 需要
    --> 处理依赖关系 cloog-ppl >= 0.15，它被软件包 gcc-4.4.7-18.el6.i686 需要
    --> 处理依赖关系 libgomp.so.1，它被软件包 gcc-4.4.7-18.el6.i686 需要
    --> 执行事务检查
    ---> Package cloog-ppl.i686 0:0.15.7-1.2.el6 will be 安装
    --> 处理依赖关系 libppl_c.so.2，它被软件包 cloog-ppl-0.15.7-1.2.el6.i686 需要
    --> 处理依赖关系 libppl.so.7，它被软件包 cloog-ppl-0.15.7-1.2.el6.i686 需要
    ---> Package cpp.i686 0:4.4.7-18.el6 will be 安装
    --> 处理依赖关系 libmpfr.so.1，它被软件包 cpp-4.4.7-18.el6.i686 需要
    ---> Package glibc-devel.i686 0:2.12-1.209.el6_9.1 will be 安装
    --> 处理依赖关系 glibc-headers = 2.12-1.209.el6_9.1，它被软件包 glibc-devel-2.12-1.209.el6_9.1.i686 需要
    --> 处理依赖关系 glibc = 2.12-1.209.el6_9.1，它被软件包 glibc-devel-2.12-1.209.el6_9.1.i686 需要
    --> 处理依赖关系 glibc-headers，它被软件包 glibc-devel-2.12-1.209.el6_9.1.i686 需要
    ---> Package libgomp.i686 0:4.4.7-18.el6 will be 安装
    --> 执行事务检查
    ---> Package glibc.i686 0:2.12-1.209.el6 will be 升级
    --> 处理依赖关系 glibc = 2.12-1.209.el6，它被软件包 glibc-common-2.12-1.209.el6.i686 需要
    ---> Package glibc.i686 0:2.12-1.209.el6_9.1 will be an update
    ---> Package glibc-headers.i686 0:2.12-1.209.el6_9.1 will be 安装
    --> 处理依赖关系 kernel-headers >= 2.2.1，它被软件包 glibc-headers-2.12-1.209.el6_9.1.i686 需要
    --> 处理依赖关系 kernel-headers，它被软件包 glibc-headers-2.12-1.209.el6_9.1.i686 需要
    ---> Package mpfr.i686 0:2.4.1-6.el6 will be 安装
    ---> Package ppl.i686 0:0.10.2-11.el6 will be 安装
    --> 执行事务检查
    ---> Package glibc-common.i686 0:2.12-1.209.el6 will be 升级
    ---> Package glibc-common.i686 0:2.12-1.209.el6_9.1 will be an update
    ---> Package kernel-headers.i686 0:2.6.32-696.1.1.el6 will be 安装
    --> 完成依赖关系计算

    依赖关系解决

    ============================================================================================================================================================
     软件包                                  架构                          版本                                          仓库                              大小
    ============================================================================================================================================================
    正在安装:
     gcc                                     i686                          4.4.7-18.el6                                  base                             8.2 M
    为依赖而安装:
     cloog-ppl                               i686                          0.15.7-1.2.el6                                base                              93 k
     cpp                                     i686                          4.4.7-18.el6                                  base                             3.4 M
     glibc-devel                             i686                          2.12-1.209.el6_9.1                            updates                          991 k
     glibc-headers                           i686                          2.12-1.209.el6_9.1                            updates                          628 k
     kernel-headers                          i686                          2.6.32-696.1.1.el6                            updates                          4.5 M
     libgomp                                 i686                          4.4.7-18.el6                                  base                             136 k
     mpfr                                    i686                          2.4.1-6.el6                                   base                             153 k
     ppl                                     i686                          0.10.2-11.el6                                 base                             1.3 M
    为依赖而更新:
     glibc                                   i686                          2.12-1.209.el6_9.1                            updates                          4.4 M
     glibc-common                            i686                          2.12-1.209.el6_9.1                            updates                           14 M

    事务概要
    ============================================================================================================================================================
    Install       9 Package(s)
    Upgrade       2 Package(s)

    总下载量：38 M
    下载软件包：
    (1/11): cloog-ppl-0.15.7-1.2.el6.i686.rpm                                                                                            |  93 kB     00:00     
    (2/11): cpp-4.4.7-18.el6.i686.rpm                                                                                                    | 3.4 MB     00:01     
    (3/11): gcc-4.4.7-18.el6.i686.rpm                                                                                                    | 8.2 MB     00:04     
    http://mirrors.nwsuaf.edu.cn/centos/6.9/updates/i386/Packages/glibc-2.12-1.209.el6_9.1.i686.rpm: [Errno 12] Timeout on http://mirrors.nwsuaf.edu.cn/centos/6.9/updates/i386/Packages/glibc-2.12-1.209.el6_9.1.i686.rpm: (28, 'Operation too slow. Less than 1 bytes/sec transfered the last 30 seconds')
    尝试其他镜像。
    (4/11): glibc-2.12-1.209.el6_9.1.i686.rpm                                                                                            | 4.4 MB     00:02     
    (5/11): glibc-common-2.12-1.209.el6_9.1.i686.rpm                                                                                     |  14 MB     00:08     
    (6/11): glibc-devel-2.12-1.209.el6_9.1.i686.rpm                                                                                      | 991 kB     00:00     
    (7/11): glibc-headers-2.12-1.209.el6_9.1.i686.rpm                                                                                    | 628 kB     00:00     
    http://mirror.lzu.edu.cn/centos/6.9/updates/i386/Packages/kernel-headers-2.6.32-696.1.1.el6.i686.rpm: [Errno 14] PYCURL ERROR 18 - "transfer closed with 4127652 bytes remaining to read"
    尝试其他镜像。
    http://mirrors.njupt.edu.cn/centos/6.9/updates/i386/Packages/kernel-headers-2.6.32-696.1.1.el6.i686.rpm: [Errno 12] Timeout on http://mirrors.njupt.edu.cn/centos/6.9/updates/i386/Packages/kernel-headers-2.6.32-696.1.1.el6.i686.rpm: (28, 'Operation too slow. Less than 1 bytes/sec transfered the last 30 seconds')
    尝试其他镜像。
    (8/11): kernel-headers-2.6.32-696.1.1.el6.i686.rpm                                                                                   | 4.5 MB     00:03     
    (9/11): libgomp-4.4.7-18.el6.i686.rpm                                                                                                | 136 kB     00:00     
    (10/11): mpfr-2.4.1-6.el6.i686.rpm                                                                                                   | 153 kB     00:00     
    (11/11): ppl-0.10.2-11.el6.i686.rpm                                                                                                  | 1.3 MB     00:00     
    ------------------------------------------------------------------------------------------------------------------------------------------------------------
    总计                                                                                                                        281 kB/s |  38 MB     02:17     
    运行 rpm_check_debug 
    执行事务测试
    事务测试成功
    执行事务
      正在安装   : kernel-headers-2.6.32-696.1.1.el6.i686                                                                                                  1/13 
      正在升级   : glibc-common-2.12-1.209.el6_9.1.i686                                                                                                    2/13 
      正在升级   : glibc-2.12-1.209.el6_9.1.i686                                                                                                           3/13 
      正在安装   : glibc-headers-2.12-1.209.el6_9.1.i686                                                                                                   4/13 
      正在安装   : glibc-devel-2.12-1.209.el6_9.1.i686                                                                                                     5/13 
      正在安装   : libgomp-4.4.7-18.el6.i686                                                                                                               6/13 
      正在安装   : mpfr-2.4.1-6.el6.i686                                                                                                                   7/13 
      正在安装   : cpp-4.4.7-18.el6.i686                                                                                                                   8/13 
      正在安装   : ppl-0.10.2-11.el6.i686                                                                                                                  9/13 
      正在安装   : cloog-ppl-0.15.7-1.2.el6.i686                                                                                                          10/13 
      正在安装   : gcc-4.4.7-18.el6.i686                                                                                                                  11/13 
      清理       : glibc-common-2.12-1.209.el6.i686                                                                                                       12/13 
      清理       : glibc-2.12-1.209.el6.i686                                                                                                              13/13 
      Verifying  : kernel-headers-2.6.32-696.1.1.el6.i686                                                                                                  1/13 
      Verifying  : glibc-devel-2.12-1.209.el6_9.1.i686                                                                                                     2/13 
      Verifying  : libgomp-4.4.7-18.el6.i686                                                                                                               3/13 
      Verifying  : gcc-4.4.7-18.el6.i686                                                                                                                   4/13 
      Verifying  : glibc-2.12-1.209.el6_9.1.i686                                                                                                           5/13 
      Verifying  : glibc-common-2.12-1.209.el6_9.1.i686                                                                                                    6/13 
      Verifying  : mpfr-2.4.1-6.el6.i686                                                                                                                   7/13 
      Verifying  : ppl-0.10.2-11.el6.i686                                                                                                                  8/13 
      Verifying  : cpp-4.4.7-18.el6.i686                                                                                                                   9/13 
      Verifying  : glibc-headers-2.12-1.209.el6_9.1.i686                                                                                                  10/13 
      Verifying  : cloog-ppl-0.15.7-1.2.el6.i686                                                                                                          11/13 
      Verifying  : glibc-common-2.12-1.209.el6.i686                                                                                                       12/13 
      Verifying  : glibc-2.12-1.209.el6.i686                                                                                                              13/13 

    已安装:
      gcc.i686 0:4.4.7-18.el6                                                                                                                                   

    作为依赖被安装:
      cloog-ppl.i686 0:0.15.7-1.2.el6            cpp.i686 0:4.4.7-18.el6       glibc-devel.i686 0:2.12-1.209.el6_9.1   glibc-headers.i686 0:2.12-1.209.el6_9.1  
      kernel-headers.i686 0:2.6.32-696.1.1.el6   libgomp.i686 0:4.4.7-18.el6   mpfr.i686 0:2.4.1-6.el6                 ppl.i686 0:0.10.2-11.el6                 

    作为依赖被升级:
      glibc.i686 0:2.12-1.209.el6_9.1                                           glibc-common.i686 0:2.12-1.209.el6_9.1                                          

    完毕！
$rpm -i|grep gcc
    rpm: no packages given for install
$rpm -qi|grep gcc
    rpm: no arguments given for query
$rpm -qa|grep gcc
    libgcc-4.4.7-18.el6.i686
    gcc-4.4.7-18.el6.i686
$./configure --prefix=/usr/local/apr-1.5.2
    checking build system type... i686-pc-linux-gnu
    checking host system type... i686-pc-linux-gnu
    checking target system type... i686-pc-linux-gnu
    Configuring APR library
    Platform: i686-pc-linux-gnu
    checking for working mkdir -p... yes
    APR Version: 1.5.2
    checking for chosen layout... apr
    checking for gcc... gcc
    checking whether the C compiler works... yes
    checking for C compiler default output file name... a.out
    checking for suffix of executables... 
    checking whether we are cross compiling... no
    checking for suffix of object files... o
    …………………………………………
    …………………………………………
    ………………好多checking………………………
    …………………………………………
    …………………………………………
    checking for working getaddrinfo... yes
    checking for negative error codes for getaddrinfo... yes
    checking for working getnameinfo... yes
    checking for sockaddr_in6... yes
    checking for sockaddr_storage... yes
    checking for working AI_ADDRCONFIG... yes
    checking if APR supports IPv6... yes
    checking langinfo.h usability... yes
    checking langinfo.h presence... yes
    checking for langinfo.h... yes
    checking for nl_langinfo... yes
      setting have_unicode_fs to "0"
      setting apr_has_xthread_files to "0"
      setting apr_procattr_user_set_requires_password to "0"
      setting apr_thread_func to ""
      setting apr_has_user to "1"

    Restore user-defined environment settings...
      restoring CPPFLAGS to ""
      setting EXTRA_CPPFLAGS to "-DLINUX -D_REENTRANT -D_GNU_SOURCE -D_LARGEFILE64_SOURCE"
      restoring CFLAGS to ""
      setting EXTRA_CFLAGS to "-g -O2 -pthread"
      restoring LDFLAGS to ""
      setting EXTRA_LDFLAGS to ""
      restoring LIBS to ""
      setting EXTRA_LIBS to "-lrt -lcrypt  -lpthread"
      restoring INCLUDES to ""
      setting EXTRA_INCLUDES to ""
    configure: creating ./config.status
    config.status: creating Makefile
    config.status: creating include/apr.h
    config.status: creating build/apr_rules.mk
    config.status: creating build/pkg/pkginfo
    config.status: creating apr-1-config
    config.status: creating apr.pc
    config.status: creating test/Makefile
    config.status: creating test/internal/Makefile
    config.status: creating include/arch/unix/apr_private.h
    config.status: executing libtool commands
    rm: cannot remove `libtoolT': No such file or directory
    config.status: executing default commands
$make
    make[1]: Entering directory `/home/ssy/apr-1.5.2'
    /home/ssy/apr-1.5.2/build/mkdir.sh tools
    /bin/sh /home/ssy/apr-1.5.2/libtool --silent --mode=compile gcc -g -O2 -pthread   -DHAVE_CONFIG_H  -DLINUX -D_REENTRANT -D_GNU_SOURCE -D_LARGEFILE64_SOURCE   -I./include -I/home/ssy/apr-1.5.2/include/arch/unix -I./include/arch/unix -I/home/ssy/apr-1.5.2/include/arch/unix -I/home/ssy/apr-1.5.2/include -I/home/ssy/apr-1.5.2/include/private -I/home/ssy/apr-1.5.2/include/private  -o tools/gen_test_char.lo -c tools/gen_test_char.c && touch tools/gen_test_char.lo
    /bin/sh /home/ssy/apr-1.5.2/libtool --silent --mode=link gcc -g -O2 -pthread   -DHAVE_CONFIG_H  -DLINUX -D_REENTRANT -D_GNU_SOURCE -D_LARGEFILE64_SOURCE   -I./include -I/home/ssy/apr-1.5.2/include/arch/unix -I./include/arch/unix -I/home/ssy/apr-1.5.2/include/arch/unix -I/home/ssy/apr-1.5.2/include -I/home/ssy/apr-1.5.2/include/private -I/home/ssy/apr-1.5.2/include/private   -no-install    -o tools/gen_test_char tools/gen_test_char.lo    -lrt -lcrypt  -lpthread
    /home/ssy/apr-1.5.2/build/mkdir.sh include/private
    tools/gen_test_char > include/private/apr_escape_test_char.h
    ……………………………………
    ……………………………………
    ……………………………………
    ……………………………………
    make[1]: Leaving directory `/home/ssy/apr-1.5.2'
$make install
    make[1]: Entering directory `/home/ssy/apr-1.5.2'
    make[1]: Nothing to be done for `local-all'.
    make[1]: Leaving directory `/home/ssy/apr-1.5.2'
    /home/ssy/apr-1.5.2/build/mkdir.sh /usr/local/apr-1.5.2/lib /usr/local/apr-1.5.2/bin /usr/local/apr-1.5.2/build-1 \
                 /usr/local/apr-1.5.2/lib/pkgconfig /usr/local/apr-1.5.2/include/apr-1
    mkdir /usr/local/apr-1.5.2
    mkdir: 无法创建目录"/usr/local/apr-1.5.2": 权限不够
    mkdir /usr/local/apr-1.5.2/lib
    mkdir: 无法创建目录"/usr/local/apr-1.5.2/lib": 没有那个文件或目录
    mkdir /usr/local/apr-1.5.2
    mkdir: 无法创建目录"/usr/local/apr-1.5.2": 权限不够
    mkdir /usr/local/apr-1.5.2/bin
    mkdir: 无法创建目录"/usr/local/apr-1.5.2/bin": 没有那个文件或目录
    mkdir /usr/local/apr-1.5.2
    mkdir: 无法创建目录"/usr/local/apr-1.5.2": 权限不够
    mkdir /usr/local/apr-1.5.2/build-1
    mkdir: 无法创建目录"/usr/local/apr-1.5.2/build-1": 没有那个文件或目录
    mkdir /usr/local/apr-1.5.2
    mkdir: 无法创建目录"/usr/local/apr-1.5.2": 权限不够
    mkdir /usr/local/apr-1.5.2/lib
    mkdir: 无法创建目录"/usr/local/apr-1.5.2/lib": 没有那个文件或目录
    mkdir /usr/local/apr-1.5.2/lib/pkgconfig
    mkdir: 无法创建目录"/usr/local/apr-1.5.2/lib/pkgconfig": 没有那个文件或目录
    mkdir /usr/local/apr-1.5.2
    mkdir: 无法创建目录"/usr/local/apr-1.5.2": 权限不够
    mkdir /usr/local/apr-1.5.2/include
    mkdir: 无法创建目录"/usr/local/apr-1.5.2/include": 没有那个文件或目录
    mkdir /usr/local/apr-1.5.2/include/apr-1
    mkdir: 无法创建目录"/usr/local/apr-1.5.2/include/apr-1": 没有那个文件或目录
    make: *** [install] 错误 1
$sudo make install
password for ssy: 
    make[1]: Entering directory `/home/ssy/apr-1.5.2'
    make[1]: Nothing to be done for `local-all'.
    make[1]: Leaving directory `/home/ssy/apr-1.5.2'
    /home/ssy/apr-1.5.2/build/mkdir.sh /usr/local/apr-1.5.2/lib /usr/local/apr-1.5.2/bin /usr/local/apr-1.5.2/build-1 \
                 /usr/local/apr-1.5.2/lib/pkgconfig /usr/local/apr-1.5.2/include/apr-1
    mkdir /usr/local/apr-1.5.2
    mkdir /usr/local/apr-1.5.2/lib
    mkdir /usr/local/apr-1.5.2/bin
    mkdir /usr/local/apr-1.5.2/build-1
    mkdir /usr/local/apr-1.5.2/lib/pkgconfig
    mkdir /usr/local/apr-1.5.2/include
    mkdir /usr/local/apr-1.5.2/include/apr-1
    /usr/bin/install -c -m 644 /home/ssy/apr-1.5.2/include/apr.h /usr/local/apr-1.5.2/include/apr-1
    for f in /home/ssy/apr-1.5.2/include/apr_*.h; do \
            /usr/bin/install -c -m 644 ${f} /usr/local/apr-1.5.2/include/apr-1; \
        done
    /bin/sh /home/ssy/apr-1.5.2/libtool --mode=install /usr/bin/install -c -m 755 libapr-1.la /usr/local/apr-1.5.2/lib
    libtool: install: /usr/bin/install -c -m 755 .libs/libapr-1.so.0.5.2 /usr/local/apr-1.5.2/lib/libapr-1.so.0.5.2
    libtool: install: (cd /usr/local/apr-1.5.2/lib && { ln -s -f libapr-1.so.0.5.2 libapr-1.so.0 || { rm -f libapr-1.so.0 && ln -s libapr-1.so.0.5.2 libapr-1.so.0; }; })
    libtool: install: (cd /usr/local/apr-1.5.2/lib && { ln -s -f libapr-1.so.0.5.2 libapr-1.so || { rm -f libapr-1.so && ln -s libapr-1.so.0.5.2 libapr-1.so; }; })
    libtool: install: /usr/bin/install -c -m 755 .libs/libapr-1.lai /usr/local/apr-1.5.2/lib/libapr-1.la
    libtool: install: /usr/bin/install -c -m 755 .libs/libapr-1.a /usr/local/apr-1.5.2/lib/libapr-1.a
    libtool: install: chmod 644 /usr/local/apr-1.5.2/lib/libapr-1.a
    libtool: install: ranlib /usr/local/apr-1.5.2/lib/libapr-1.a
    libtool: finish: PATH="/sbin:/bin:/usr/sbin:/usr/bin:/sbin" ldconfig -n /usr/local/apr-1.5.2/lib
    ----------------------------------------------------------------------
    Libraries have been installed in:
       /usr/local/apr-1.5.2/lib

    If you ever happen to want to link against installed libraries
    in a given directory, LIBDIR, you must either use libtool, and
    specify the full pathname of the library, or use the `-LLIBDIR'
    flag during linking and do at least one of the following:
       - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
         during execution
       - add LIBDIR to the `LD_RUN_PATH' environment variable
         during linking
       - use the `-Wl,-rpath -Wl,LIBDIR' linker flag
       - have your system administrator add LIBDIR to `/etc/ld.so.conf'

    See any operating system documentation about shared libraries for
    more information, such as the ld(1) and ld.so(8) manual pages.
    ----------------------------------------------------------------------
    /usr/bin/install -c -m 644 apr.exp /usr/local/apr-1.5.2/lib/apr.exp
    /usr/bin/install -c -m 644 apr.pc /usr/local/apr-1.5.2/lib/pkgconfig/apr-1.pc
    for f in libtool shlibtool; do \
            if test -f ${f}; then /usr/bin/install -c -m 755 ${f} /usr/local/apr-1.5.2/build-1; fi; \
        done
    /usr/bin/install -c -m 755 /home/ssy/apr-1.5.2/build/mkdir.sh /usr/local/apr-1.5.2/build-1
    for f in make_exports.awk make_var_export.awk; do \
            /usr/bin/install -c -m 644 /home/ssy/apr-1.5.2/build/${f} /usr/local/apr-1.5.2/build-1; \
        done
    /usr/bin/install -c -m 644 build/apr_rules.out /usr/local/apr-1.5.2/build-1/apr_rules.mk
    /usr/bin/install -c -m 755 apr-config.out /usr/local/apr-1.5.2/bin/apr-1-config
$rpm -q apr
    package apr is not installed                                                #####不是rpm安装，编译源代码安装的没有检测到
$rpm -qa|grep apr
    空
$./configure prefix=/usr/local/httpd-2.4.25 --with-apr=/usr/local/apr-1.5.2     ####安装上还提示找不到APR，指定路径
    checking for chosen layout... Apache
    checking for working mkdir -p... yes
    checking for grep that handles long lines and -e... /bin/grep
    checking for egrep... /bin/grep -E
    checking build system type... i686-pc-linux-gnu
    checking host system type... i686-pc-linux-gnu
    checking target system type... i686-pc-linux-gnu
    configure: 
    configure: Configuring Apache Portable Runtime library...
    configure: 
    checking for APR... yes
      setting CC to "gcc"
      setting CPP to "gcc -E"
      setting CFLAGS to " -g -O2 -pthread"
      setting CPPFLAGS to " -DLINUX -D_REENTRANT -D_GNU_SOURCE -D_LARGEFILE64_SOURCE"
      setting LDFLAGS to " "
    configure: 
    configure: Configuring Apache Portable Runtime Utility library...
    configure: 
    checking for APR-util... no
    configure: error: APR-util not found.  Please read the documentation.
$tar -xvf apr-util-1.5.4.tar.gz
    ………………
$./configure prefix=/usr/local/apr-util-1.5.4 --with-apr=/usr/local/apr-1.5.2
    ………………
$make
    ………………
$sudo make install
    ………………
[ssy@localhost ~/pcre-8.00]$./configure --prefix=/usr/local/pcre-8.00
    ………………
[ssy@localhost ~/pcre-8.00]$make distclean          ###pcre版本安装的低了
     rm -f pcretest pcretest
     rm -f pcregrep pcregrep
    test -z "pcre_chartables.c testsavedregex teststderr testtry testNinput" || rm -f pcre_chartables.c testsavedregex teststderr testtry testNinput
    test -z "libpcre.la libpcreposix.la libpcrecpp.la" || rm -f libpcre.la libpcreposix.la libpcrecpp.la
    rm -f "./so_locations"
    rm -f "./so_locations"
    rm -f "./so_locations"
    rm -rf .libs _libs
     rm -f pcrecpp_unittest pcrecpp_unittest
     rm -f pcre_scanner_unittest pcre_scanner_unittest
     rm -f pcre_stringpiece_unittest pcre_stringpiece_unittest
    rm -f *.o
    rm -f *.lo
    rm -f *.tab.c
    test -z "libpcre.pc libpcreposix.pc libpcrecpp.pc pcre-config pcre.h pcre_stringpiece.h pcrecpparg.h" || rm -f libpcre.pc libpcreposix.pc libpcrecpp.pc pcre-config pcre.h pcre_stringpiece.h pcrecpparg.h
    rm -f config.h stamp-h1
    rm -f libtool config.lt
    rm -f TAGS ID GTAGS GRTAGS GSYMS GPATH tags
    rm -f config.status config.cache config.log configure.lineno config.status.lineno
    rm -rf ./.deps
    rm -f Makefile
[ssy@localhost ~/pcre-8.00]$make distclean
    make: *** 没有规则可以创建目标“distclean”。 停止。
[ssy@localhost ~/pcre-8.00]$gmake confclean
    gmake: *** 没有规则可以创建目标“confclean”。 停止。
[ssy@localhost ~/pcre-8.40]$./configure --prefix=/usr/local/pcre-8.40
    ……………………
    ……………………
    configure: error: You need a C++ compiler for C++ support.
[ssy@localhost ~/pcre-8.40]$sudo yum -y install gcc-c++
    已安装:
    gcc-c++.i686 0:4.4.7-18.el6                                                                                                                               作为依赖被安装:
    libstdc++-devel.i686 0:4.4.7-18.el6                                                                                                                       
[ssy@localhost ~/pcre-8.40]$./configure --prefix=/usr/local/pcre-8.40
    ………………………………
    ………………………………
[ssy@localhost ~/pcre-8.40]$sudo make & sudo make install
    libtool: warning: relinking 'libpcreposix.la'
```

## PHP

```bash
[ssy@localhost ~/php-7.1.5]$./configure --prefix=/usr/local/php-7.1.5
    ………………………………
    ………………………………
    configure: error: xml2-config not found. Please check your libxml2 installation.
[ssy@localhost ~/php-7.1.5]$sudo yum -y install libxml2
    包 libxml2-2.7.6-21.el6_8.1.i686 已安装并且是最新版本
    无须任何处理
[ssy@localhost ~/php-7.1.5]$rpm -qa | grep xml
    libxml2-2.7.6-21.el6_8.1.i686
[ssy@localhost ~/php-7.1.5]$yum list|grep libxml2
    libxml2.i686                               2.7.6-21.el6_8.1              @anaconda-CentOS-201703281202.i386/6.9
    libxml2-devel.i686                         2.7.6-21.el6_8.1              base   
    libxml2-python.i686                        2.7.6-21.el6_8.1              base   
    libxml2-static.i686                        2.7.6-21.el6_8.1              base   
[ssy@localhost ~/php-7.1.5]$sudo yum -y install libxml2-devel
    已安装:
      libxml2-devel.i686 0:2.7.6-21.el6_8.1                                                                                                                     
    作为依赖被安装:
      zlib-devel.i686 0:1.2.3-29.el6 
[ssy@localhost ~/php-7.1.5]$sudo find / -name 'xml2-config'
    /usr/bin/xml2-config         ###虽然libxml2相关有4个安装包，
                                 ###且提示的是libxml2安装包，
                                 ###但装完libxml2-devel后，原来提示找不到的文件xml2-config已找到
[ssy@localhost ~/php-7.1.5]$./configure --prefix=/usr/local/php-7.1.5
    Build complete.
    Don't forget to run 'make test'.
[ssy@localhost ~/php-7.1.5]$make test
    ……………………………………………………
    ……………………………………………………
    =====================================================================
    TEST RESULT SUMMARY
    ---------------------------------------------------------------------
    Exts skipped    :   48
    Exts tested     :   26
    ---------------------------------------------------------------------

    Number of tests : 15366             10253
    Tests skipped   : 5113 ( 33.3%) --------
    Tests warned    :    0 (  0.0%) (  0.0%)
    Tests failed    :    5 (  0.0%) (  0.0%)
    Expected fail   :   35 (  0.2%) (  0.3%)
    Tests passed    : 10213 ( 66.5%) ( 99.6%)
    ---------------------------------------------------------------------
    Time taken      : 10633 seconds
    =====================================================================

    =====================================================================
    EXPECTED FAILED TEST SUMMARY
    ---------------------------------------------------------------------
    Test open_basedir configuration [tests/security/open_basedir_linkinfo.phpt]  XFAIL REASON: BUG: open_basedir cannot delete symlink to prohibited file. See also
    bugs 48111 and 52176.

    =====================================================================
    FAILED TEST SUMMARY
    ---------------------------------------------------------------------
    Concatenating many small strings should not slowdown allocations [Zend/tests/concat_003.phpt]
    array dns_get_record ( string $hostname [, int $type = DNS_ANY [, array &$authns [, array &$addtl [, bool &$raw = false ]]]] ); [ext/standard/tests/dns_get_record.phpt]
    Bug #60120 proc_open hangs with stdin/out with 2048+ bytes [ext/standard/tests/streams/proc_open_bug60120.phpt]
    Bug #64438 proc_open hangs with stdin/out with 4097+ bytes [ext/standard/tests/streams/proc_open_bug64438.phpt]
    Bug #69900 Commandline input/output weird behaviour with STDIO [ext/standard/tests/streams/proc_open_bug69900.phpt]
    =====================================================================

    ###make test 就是 PHP 的自动化测试
    ###会出现许多 skip 和 fail 说明有一些扩展没有安装，相关环境的配置也需要优化
    ###经测试，make test之后就无需make了，再make时还是会提示要make test，不知可否跳过make test，直接make install

[ssy@localhost ~/php-7.1.5]$sudo make install



============================PHP安装完成了===============================================


============================开始安装MySQL===============================================

版本：
    只有Community版本MySQL是免费的

两种包：
    RPM BUNDLE  是把MySQL的所有RPM包都包括了

    RPM PACKAGE 没个MySQL部件一个包，安装哪个下载哪个
        如mysql-community-devel-5.7.18-1.el6.i686.rpm是开发库包devel---development library

MySQL最基础安装需要四个MySQL包：

    common
        完整名：RPM Package, MySQL Configuration
        文件名：mysql-community-common-5.7.18-1.el6.i686.rpm
    lib
        完整名：RPM Package, Shared Libraries
        文件名：mysql-community-libs-5.7.18-1.el6.i686.rpm
        注：是Shared Libraries，不是Development Libraries
    client
        完整名：RPM Package, Client Utilities
        文件名：mysql-community-client-5.7.18-1.el6.i686.rpm
    server
        完整名：RPM Package, MySQL Server
        文件名：mysql-community-server-5.7.18-1.el6.i686.rpm

还额外需要两个其他包：

    per

    numactl - 用于控制 进程与共享存储的 NUMA 技术机制

依赖关系如下：

    common<-lib<-client<-server
                numactl<-
                    per<-


所以安装顺序：

    [ssy@localhost ~/mysql]$sudo rpm -ivh mysql-community-common-5.7.18-1.el6.i686.rpm 
    [ssy@localhost ~/mysql]$sudo rpm -ivh mysql-community-libs-5.7.18-1.el6.i686.rpm 
    [ssy@localhost ~/mysql]$sudo rpm -ivh mysql-community-client-5.7.18-1.el6.i686.rpm 
    [ssy@localhost ~/mysql]$sudo rpm -ivh mysql-community-server-5.7.18-1.el6.i686.rpm 
    [ssy@localhost ~/mysql]$sudo yum -y install numactl
    [ssy@localhost ~/mysql]$sudo yum -y install perl

    试一下本地yum，让yum自己解决依赖关系：

    [ssy@localhost ~/mysql]$sudo yum localinstall MySQL-server-5.6.21-1.el6.i686.rpm

安装时会提示NOKEY，不过不影响安装

    rpm -ivh mysql-community-common-5.7.18-1.el6.i686.rpm
        warning: mysql-community-server-5.7.18-1.el6.i686.rpm: Header V3 DSA/SHA1 Signature, key ID 5072e1f5: NOKEY

主动去验证签名也出错

    rpm -K mysql-community-common-5.7.18-1.el6.i686.rpm
        mysql-community-common-5.7.18-1.el6.i686.rpm: (SHA1) DSA sha1 md5 (GPG) NOT OK (MISSING KEYS: GPG#5072e1f5) 

导入公钥到GPG库

    1、
        从上面的提示知道：MySQL的公钥ID是5072e1f5
        可从http://pgp.mit.edu/找到MySQL的公钥：
            命名的键为：mysql-build@oss.oracle.com
            公钥ID为：5072E1F5
            标注为：selfsig
        找到后复制到剪贴板，执行命令

        [ssy@localhost ~/mysql]$touch mysql_pubkey.asc 
            #建立公钥文件
        [ssy@localhost ~/mysql]$vi mysql_pubkey.asc 
            #粘贴从网上下载的公共秘钥
        [ssy@localhost ~/mysql]$gpg --import mysql_pubkey.asc 
            gpg: /home/ssy/.gnupg/trustdb.gpg：建立了信任度数据库
            gpg: 密钥 5072E1F5：公钥“MySQL Release Engineering <mysql-build@oss.oracle.com>”已导入
            gpg: 合计被处理的数量：1
            gpg:           已导入：1
            gpg: 没有找到任何绝对信任的密钥
    2、
        [ssy@localhost ~/mysql]$gpg --recv-keys 5072E1F5
            gpg: 下载密钥‘5072E1F5’，从 hkp 服务器 keys.gnupg.net
            gpg: 公钥服务器超时
            gpg: 从公钥服务器接收失败：Keyserver error


把公钥导入到RPM

    1、从文件导入

        没有公钥文件，则从PGP库导出到文件

            [ssy@localhost ~/mysql]$sudo gpg --export -a 5072e1f5 > 5072e1f5.asc

        rpm从公钥文件导入
            [ssy@localhost ~/mysql]$sudo rpm --import 5072e1f5.asc
            #导入成功没有提示，出错才提示

    2、通过URL导入

        [ssy@localhost ~/mysql]$rpm --import http://dev.mysql.com/doc/refman/5.7/en/checking-gpg-signature.html

公钥导入RPM后再次验证OK

    [ssy@localhost ~/mysql]$rpm -K mysql-community-common-5.7.18-1.el6.i686.rpm 
        mysql-community-common-5.7.18-1.el6.i686.rpm: (sha1) dsa sha1 md5 gpg OK


=====================mysql配置======================

mysql安装过程中创建了名为'mysql'的用户和名为'mysql'的用户组

mysql安装成功后服务已自动运行

查看进程

    [ssy@localhost ~]$ ps -le | grep mysql
        4 S     0  1255     1  0  80   0 -  1568 ?      ?        00:00:00 mysqld_safe
        4 S    27  1479  1255  4  80   0 - 154200 ?     ?        00:29:15 mysqld

查看端口

    [ssy@localhost ~]$ sudo netstat -tunlp
        Active Internet connections (only servers)
        Proto Recv-Q Send-Q Local Address               Foreign Address             State       PID/Program name   
        tcp        0      0 0.0.0.0:22                  0.0.0.0:*                   LISTEN      1108/sshd           
        tcp        0      0 :::3306                     :::*                        LISTEN      1479/mysqld         
        tcp        0      0 :::22                       :::*                        LISTEN      1108/sshd           
        udp        0      0 0.0.0.0:68                  0.0.0.0:*                               958/dhclient        
    [ssy@localhost ~]$ sudo netstat -tunlp | grep mysql
        tcp        0      0 :::3306                     :::*                        LISTEN      1479/mysqld         

mysql为root创建了临时密码，写入到了日志文件里/var/log/mysqld.log

    [ssy@localhost ~]$ vi /var/log/mysqld.log
        2017-05-22T22:39:51.124727Z 1 [Note] A temporary password is generated for root@localhost: fH_hdqJDS9rh

先使用root账号和临时密码登录mysql

    [ssy@localhost ~]$ mysql -u root -pfH_hdqJDS9rh
        mysql: [Warning] Using a password on the command line interface can be insecure.

然后发现执行什么命令都提示你改密码

    mysql> select 1;
    ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.

修改的密码需要符合安全规范

    mysql> alter user 'root'@'localhost' identified by '123798';
    ERROR 1819 (HY000): Your password does not satisfy the current policy requirements

改为复杂密码，注意是修改root账号的密码，不是mysql账号的密码

    mysql> alter user 'root'@'localhost' identified by  '(Shen99$)';
    Query OK, 0 rows affected (0.01 sec)

可以执行了

    mysql> select 1;
    +---+
    | 1 |
    +---+
    | 1 |
    +---+
    1 row in set (0.00 sec)
