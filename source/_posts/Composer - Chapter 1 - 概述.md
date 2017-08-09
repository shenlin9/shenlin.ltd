title: Composer - Chapter 1 - 概述
categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer

---

Composer 是 PHP 的依赖管理工具，它允许你声明自己的项目所依赖的库，而它将根据你的声明为你的项目安装、更新这些儿库。

<!--more-->

## 概念

composer涉及的几个概念：

* project 项目
* package 包
* platform package 平台软件包
* library 库
* repository 资源库
* composer

### package

包本质上就是一个包含各种内容的目录，但为了方便分发而把多个文件归档成了一个文件，再加上描述信息，就是一个包，而包里的内容可能是xx库或xx图片或xx软件。每个包都有描述信息，必不可少的描述项目是名字和版本号，使用它们来区分识别包。

composer包额外包含一些儿元数据，如 source 源的定义，指明了哪里获得资源包。

把一个 composer.json 文件放在目录中，那么整个目录就是一个 composer 包，composer.json 文件所在的项目根目录就是 root 包。

    一个包是不是“root 包”，取决于它的上下文。 例：如果项目依赖 monolog 库，那么这个项目就是“root 包”。 但是，如果从 GitHub 上克隆了 monolog 为它修复 bug， 那么此时 monolog 就是“root 包”。

composer 包通过在 composer.json 文件中添加 require 属性定义依赖，
同时，
composer 包通过添加 name 属性提供依赖给其他项目的 composer 使用，

是否提供依赖给其他项目的 composer 安装就在于有没有 name 属性，没有 name 属性则表示不提供依赖。

### platform package

1. 平台软件包是指对于 composer 来说不可安装但已经安装好的软件包，包括：

* `php` 
    指用户的php，可以对其版本做限制，如：>=5.3.0

* `HHVM`
    指facebook的HHVM (HipHop Virtual Machine)，可对其版本进行限制，如：>=2.3.3

* `ext-<name>`
    指php的扩展，包括核心扩展，如ext-bcmath，扩展的版本通常是不一致的，一般约束为 *

* `lib-<name>`
    指php的库，如lib-openssl，可对版本进行限制

2. 列出 php 扩展对项目来说很是重要的，一些儿看起来是标准的扩展可能在部署机器上并不存在，如最小化安装的 CentOS 不包括 ext-mysqli，结果就是用户在部署安装项目时 composer 没有错误，但在运行时出错.

3. 平台软件包的列表可以通过 composer 命令获取：
```
$ composer show --platform
composer-plugin-api 1.1.0    The Composer Plugin API
…………
…………
ext-xml             7.1.5    The xml PHP extension
ext-xmlreader       7.1.5    The xmlreader PHP extension
ext-xmlwriter       7.1.5    The xmlwriter PHP extension
ext-zlib            7.1.5    The zlib PHP extension
lib-iconv           2.12     The iconv PHP library
lib-libxml          2.7.6    The libxml PHP library
lib-openssl         1.0.1.5  OpenSSL 1.0.1e-fips 11 Feb 2013
lib-pcre            8.38     The pcre PHP library
php                 7.1.5    The PHP interpreter
php-ipv6            7.1.5    The PHP interpreter, with IPv6 support
php-zts             7.1.5    The PHP interpreter, with Zend Thread Safety
```


### library

将一个包解档后，得到的内容可能是图片，可直接查看；也可能是软件，可双击独立运行。也可能是**库**，需要其他程序调用才能起作用；

所以库只是包内容的一种，包是库的分发形式。

程序运行依赖的是库，通过下载、安装包的形式来获得库。

    包的描述信息中既包含内容的描述信息也包自身的描述信息，如：
     
    name        内容名 
    version     内容版本
    packager    打包软件
    build date  打包时间
    build host  打包主机

包里的内容不都是以文件的形式存在的，如metapackage，不包含任何文件不会向文件系统写入任何东西，是一个空包，包含的是依赖需求并会触发依赖的安装。

### repository

资源库是包的来源。它是一个 `包名/版本号` 的列表。

默认只有 packgist.org 是在 composer 中注册的。

资源库的定义仅可用于“root 包”，而在你依赖的包中定义的资源库将不会被加载。

### composer

composer是php的依赖管理工具，它允许你声明自己的项目所依赖的库，而它将根据你的声明为你的项目安装、更新这些儿库。

composer不是像Yum一样的包管理器，虽然它也管理包和库，但它是基于项目进行管理包和库的，会把包和库安装到项目下的文件夹里，默认情况下不安装任何全局性的东西（为了方便有个global命令）。

