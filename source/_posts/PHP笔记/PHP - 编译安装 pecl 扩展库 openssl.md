---
title: PHP - 编译安装 pecl 扩展库 openssl
categories:
  - PHP
tags:
  - PECL
  - OpenSSL
---

http://php.net/manual/zh/install.pecl.intro.php

和安装普通源码包差不多，都需要进入扩展的源码目录
然后执行 `./configure , make , make install`

但php的pecl扩展库需要在 `./configure` 之前先执行 phpize， phpize 命令是用来
准备 PHP 扩展库的编译环境的，并生成 configure 文件

<!--more-->

phpize位于php安装目录bin下，不是源码目录

phpize需要的config.m4文件就位于扩展源码目录下，一般文件名为config.0m4，需要改个名

`./configure` 时需要选项：`--with-PeclExtName` 和 `--with-php-config=/php安装目录/bin/php-config`
php-config是提供php的安装环境变量的

## 基本步骤

```bash
$ cd Pecl扩展库源码目录
$ mv config.0m4 config.m4
$ phpize
$ ./configure --with-PeclExtName --with-php-config=/php安装目录/bin/php-config
$ make
$ sudo make install
```

安装完后，需要进入 `php.ini` 增加一行 `extension = extname.so` 以启用

## 举例说明

下面是安装openssl的例子

查询openssl的状态
```bash
[ssy@localhost ~]$ php -i|grep openssl
    OpenSSL support => disabled (install ext/openssl)
    OLDPWD => /home/ssy/php-7.1.5/ext/openssl
    $_SERVER['OLDPWD'] => /home/ssy/php-7.1.5/ext/openssl
    $_ENV['OLDPWD'] => /home/ssy/php-7.1.5/ext/openssl
```

进入openssl的源码目录
```bash
[ssy@localhost ~]$ cd php-7.1.5/ext/openssl
```

为PHP扩展准备编译环境
```bash
[ssy@localhost openssl]$ phpize
    Cannot find config.m4. 
    Make sure that you run '/usr/local/php7.1.5/bin/phpize' in the top level source directory of the module
```

为 phpize 命令准备好 config.m4 文件
```bash
[ssy@localhost openssl]$ mv config0.m4 config.m4
```

为PHP扩展准备编译环境
```bash
[ssy@localhost openssl]$ phpize
    Cannot find autoconf. Please check your autoconf installation and the
    $PHP_AUTOCONF environment variable. Then, rerun this script.
```

安装autoconf
```bash
[ssy@localhost openssl]$ sudo yum install -y autoconf
```

为PHP扩展准备编译环境并生成configure文件
```bash
[ssy@localhost openssl]$ phpize
    Configuring for:
    PHP Api Version:         20160303
    Zend Module Api No:      20160303
    Zend Extension Api No:   320160303
```

生成Makefile
```bash
[ssy@localhost openssl]$ ./configure --with-openssl --with-php-config=/usr/local/php7.1.5/bin/php-config 
    configure: error: Cannot find OpenSSL's <evp.h>
```

安装opessl-devel
```bash
[ssy@localhost openssl]$ sudo yum install -y openssl-devel
```

生成Makefile
```bash
[ssy@localhost openssl]$ ./configure --with-openssl --with-php-config=/usr/local/php7.1.5/bin/php-config 
    config.status: creating config.h
```

编译
```bash
[ssy@localhost openssl]$ make
```

安装
```bash
[ssy@localhost openssl]$ sudo make install
```

## 启用扩展

更改PHP.ini文件启用扩展
```bash
[ssy@localhost zlib]$ sudo vi /usr/local/php7.1.5/etc/php.ini
    ;extension = openssl.so
```

查询openssl的状态
```bash
[ssy@localhost openssl]$ php -i|grep openssl
    openssl
    Openssl default config => /etc/pki/tls/openssl.cnf
    openssl.cafile => no value => no value
    openssl.capath => no value => no value
    PWD => /home/ssy/php-7.1.5/ext/openssl
    $_SERVER['PWD'] => /home/ssy/php-7.1.5/ext/openssl
```

php-config的输出
```bash
[ssy@localhost testdir]$ /usr/local/php7.1.5/bin/php-config
    Usage: /usr/local/php7.1.5/bin/php-config [OPTION]
    Options:
      --prefix            [/usr/local/php7.1.5]
      --includes          [-I/usr/local/php7.1.5/include/php -I/usr/local/php7.1.5/include/php/main -I/usr/local/php7.1.5/include/php/TSRM -I/usr/local/php7.1.5/include/php/Zend -I/usr/local/php7.1.5/include/php/ext -I/usr/local/php7.1.5/include/php/ext/date/lib]
      --ldflags           []
      --libs              [-lcrypt   -lresolv -lcrypt -lrt -lrt -lm -ldl -lnsl  -lpthread -lxml2 -lz -lm -lxml2 -lz -lm -lxml2 -lz -lm -lcrypt -lxml2 -lz -lm -lxml2 -lz -lm -lxml2 -lz -lm -lcrypt ]
      --extension-dir     [/usr/local/php7.1.5/lib/php/extensions/no-debug-zts-20160303]
      --include-dir       [/usr/local/php7.1.5/include/php]
      --man-dir           [/usr/local/php7.1.5/php/man]
      --php-binary        [/usr/local/php7.1.5/bin/php]
      --php-sapis         [ apache2handler cli phpdbg cgi]
      --configure-options [--prefix=/usr/local/php7.1.5/ --with-config-file-path=/usr/local/php7.1.5/etc/ --with-apxs2=/usr/local/httpd-2.4.25/bin/apxs --enable-mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --enable-bcmath --enable-mbstring]
      --version           [7.1.5]
      --vernum            [70105]
```


