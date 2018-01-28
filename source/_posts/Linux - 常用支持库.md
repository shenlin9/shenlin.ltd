---
title: linux - 常用支持库
categories:
  - Linux
tags:
  - Linux
---

主机编译程序源码的环境，包括 C 语言、 C++语言、 Perl 语言的编译器，以及各种常见的编译支持函数库程序

<!--more-->

```bash
sudo yum install -y apr* autoconf automake
sudo yum install -y bison bzip2 bzip2*
sudo yum install -y compat* cpp curl curl-devel cmake 
sudo yum install -y fontconfig fontconfig-devel freetype freetype* freetypedevel
sudo yum install -y gcc gcc-c++ gd gettext gettext-devel glibc
sudo yum install -y kernel kernel-headers keyutils keyutils-libs-devel krb5-devel
sudo yum install -y libcom_err-devel libpng libpng-devel libjpeg* libsepoldevel libselinux-devel libstdc++-devel libtool* libgomp libxml2 libxml2-devel libXpm* libtiff libtiff* libmcrypt libvpx libgd 
sudo yum install -y make mpfr
sudo yum install -y ncurses* ntp
sudo yum install -y openssl openssl-devel
sudo yum install -y patch pcre pcre-devel perl php-common php-gd policycoreutils
sudo yum install -y telnet t1lib t1lib* tiff
sudo yum install -y nasm nasm*
sudo yum install -y wget
sudo yum install -y zlib zlib-devel
```

???有个问题：libjpeg 库和 jpegsrc 的关系
jpegsrc 库没有 RPM 包，需要通过源码编译安装
源码下载地址：http://www.ijg.org/
```bash
[shenlin@t460p ~]$ sudo wget -O /usr/local/src/jpegsrc.v9c.tar.gz http://www.ijg.org/files/jpegsrc.v9c.tar.gz
```

```bash
sudo yum install -y yasm

[shenlin@t460p ~]$ sudo yum install -y yasm
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.neusoft.edu.cn
 * extras: mirrors.neusoft.edu.cn
 * updates: mirrors.neusoft.edu.cn
No package yasm available.
Error: Nothing to do
```


apr*
autoconf
automake
bison
bzip2
bzip2*
compat*
cpp
curl
curl-devel
fontconfig
fontconfig-devel
freetype
freetype*
freetypedevel
gcc
gcc-c++
gd
gettext
gettext-devel
glibc
kernel
kernel-headers
keyutils
keyutils-libs-devel
krb5-devel
libcom_err-devel
libpng
libpng-devel
libjpeg*
libsepoldevel
libselinux-devel
libstdc++-devel
libtool*
libgomp
libxml2
libxml2-devel
libXpm*
libtifflibtiff*
make
mpfr
ncurses*
ntp
openssl
openssl-devel
patch
pcre
pcre-devel
perl
php-common
php-gd
policycoreutils
telnet
t1lib
t1lib*
nasm
nasm*
wget
zlib
zlib-devel

libmcrypt
cmake
libvpx
libgd
tiff
