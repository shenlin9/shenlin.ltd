title: Composer - Chapter 2 - 安装
categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer

---

安装 Composer 简介。
    
<!--more-->

## 系统需求

php5.3.2+以上版本
:   php的一些儿设置和编译时的选项是必须的，如启用openssl,zlib支持，有不兼容的地方时，安装程序installer会提出警告

VCS:Version Control System
:   资源库的一种形式是源码库，composer将直接对接目标库的VCS获取源码进行安装，而你也必须安装目标库对应的VCS，常见VCS软件有：git,subversion,hg,fossil

## 安装

### 1. 下载安装程序：

```
$ wget https://getcomposer.org/installer
```
或
```
$ php -r "readfile('https://getcomposer.org/installer');" | php
```

### 2. 运行安装程序：

installer是php文件

```
$ php installer
```

也可以指定安装目录和文件名：

```
$ php installer --install-dir=~/myproject/ --filename=composer
```

installer将会检测php的设置和编译时的选项，如是否安装并启用openssl,zlib等，任何不兼容都会提出警告，因此不建议直接下载composer.phar

检测通过后将下载composer.phar文件到当前目录

上面两步也可以这样一步执行

```
$ curl -sS https://getcomposer.org/installer | php
```

### 3. 运行composer

composer.phar是php的归档文件，可直接运行，无需php解析

```
$ ./composer
```

将composer目录加入$PATH 或 直接移入bin目录使用更方便

```
$ mv composer.phar /usr/local/bin/composer
$ composer
```

composer运行正常的话则会返回一个可用命令的列表








source源定义分两种：dist和source

    dist 指向已经发行的稳定版本的资源库
    source 指向开发中源代码库
    

可以直接命令行安装库：
```
$ composer require mustache/mustache
```
发现composer自动更改了 composer.json 和 composer.lock 文件，增加了新依赖的信息




