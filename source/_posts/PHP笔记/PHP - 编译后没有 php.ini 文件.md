---
title: PHP - 编译后没有 php.ini 文件
categories:
  - PHP
tags:
  - PHP.ini
---

PHP 编译后没有 php.ini 文件

<!--more-->

查看装载的php.ini路径
```bash
$ php -i | grep "Loaded Configuration File"
```

源码编译安装的php没有php.ini文件，是因为configure时少个路径参数
```bash
--with-config-file-path=/usr/local/php-7.1.5/lib
```

已经编译完成可以从源码目录cp过来
```bash
$ cp ~/php-7.1.5/php.ini-development /usr/local/php-7.1.5/lib/php.ini
```

重新编译时必须执行make clean命令
```bash
$ ./configure
$ make clean
$ make
$ make install
```

php.ini文件的变迁
```bash
php-dist.ini
php-recommended.ini

php.ini-development
php.ini-product
```

/*
 * PHP 配置
 *  配置文件：php.in
 *  1. 模块加载：
 *   extension = php_mysql.dll
 *  2. 修改模块的目录
 *    extension_dir = "D:/php/ext"
 *   也可以将 D:/php ,D:/php/ext 添加到系统环境变量中
 *  3. 在Apache中配置php
 *     更改httpd.conf
 *   LoadModule php5_module "D:/php/php5apache2_2.dll 添加PHP模块
 *   PHPIniDir "D:/php" 配置php.in路径
 *   配置AddType
 *   AddType application/x-httpd-php .php
 *   AddType application/x-httpd-php .txt
 *   *   
 *  4. register_globals = Off 设置是否开启全局变量
 *  若设置为On
 *  已GET/POST提交的参数，直接可以使用变量用调用， 建议不开启
 *  5.设置时区：date.timezone =PRC 
 *  6.设置session 
 */
