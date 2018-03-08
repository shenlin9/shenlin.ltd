---
title: PHP - configure 选项
categories:
  - PHP
tags:
  - configure
---

PHP configure 选项

<!--more-->

```bash
[ssy@localhost php-7.1.5]$ ./configure \
--prefix=/usr/local/php7.1.5/ 
--with-config-file-path=/usr/local/php7.1.5/etc/ 
--with-apxs2=/usr/local/httpd-2.4.25/bin/apxs 
--enable-mysqlnd 
--with-mysqli=mysqlnd 
--with-pdo-mysql=mysqlnd 
--enable-bcmath 
--enable-mbstring
```

PHP和服务器连接两种方法：
1. 作为模块：通过PHP的模块接口SAPI作为web服务器的模块，支持的服务器如 apache
2. 可执行程序：作为CGI或FastCGI处理器

**--enable-fpm**

激活 FPM(FastCGI Process Manager) 支持

以下为 FPM 编译的具体配置参数（全部为可选参数）：

   --with-fpm-user - 设置 FPM 运行的用户身份（默认 - nobody）

   --with-fpm-group - 设置 FPM 运行时的用户组（默认 - nobody）

   --with-fpm-systemd - 启用 systemd 集成 (默认 - no)

   --with-fpm-acl - 使用POSIX 访问控制列表 (默认 - no) 5.6.5版本起有效

详细配置：http://php.net/manual/zh/install.fpm.configuration.php




--with-mysql=/usr/bin/mysql_config  \


**--with-apxs2=FILE**

Build shared Apache 2.0 Handler module. FILE is the optional pathname to the Apache apxs tool apxs

没有 --with-apxs2 就不会生成 libphp5.so


**--enable-mysqlnd**
Enable mysqlnd explicitly, will be done implicitly when required by other extensions


**--with-mysqli=FILE**
Include MySQLi support.
FILE is the path to mysql_config.
If no value or mysqlnd is passed as FILE, the MySQL native driver will be used

    --with-mysqli=mysqlnd \
    --with-mysqli=/usr/local/mysql/bin/mysql_config \

    mysql_config 由 mysql_xxx_client 或 mysql_xxx_devel 提供，mysql_xxx_server 不提供

    这里的 xxx 指 community 或 enterprise

**--with-pdo-mysql=DIR**
PDO: MySQL support. 
DIR is the MySQL base directory
If no value or mysqlnd is passed as DIR, the MySQL native driver will be used

    --with-pdo-mysql=mysqlnd \
    --with-pdo-mysql=/usr/local/mysql \

如果之前编译 `make` 失败,记得用 `make clean` 或者 `make distclean` 清除之前编译的缓存文件,然后再重新 `make && make install`

./configure \
--prefix=/usr/local/php \
--with-config-file-path=/usr/local/php/etc \
--with-fpm-user=www \
--with-fpm-group=www \
--enable-fpm \
--enable-opcache \
--with-mysql=mysqlnd \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--disable-fileinfo \
--with-iconv-dir=/usr/local \
--with-freetype-dir \
--with-jpeg-dir \
--with-png-dir \
--with-zlib \
--with-libxml-dir=/usr \
--enable-xml \
--disable-rpath \
--enable-bcmath \
--enable-shmop \
--enable-exif \
--enable-sysvsem \
--enable-inline-optimization \
--with-curl \
--enable-mbregex \
--enable-mbstring \
--with-mcrypt \
--with-gd \
--enable-gd-native-ttf \
--with-openssl \
--with-mhash \
--enable-pcntl \
--enable-sockets \
--with-xmlrpc \
--enable-ftp \
--with-gettext \
--enable-zip \
--enable-soap \
--disable-ipv6 \
--disable-debug

