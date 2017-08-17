title: Composer - Chapter X - Composer.json Schema
categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer

---

composer 在内部是将同一个包的不同版本都看做是独立不相干的包，这点对于使用 Composer 不重要，但更改 Composer 时这点儿很重要。

## 目录结构

全局配置文件
config.json

局部配置文件
composer.json

单独的身份认证文件
auth.json

composer.lock
/vendor

## composer install 后的目录结构

    MyProject
        |--file1.php
        |--…………
        |--installer
        |--composer
        |--composer.json
        |--composer.lock
        |--vendor
            |--monolog(安装的依赖库)
            |--autoload.php
            |--composer
                |--autoload_classmap.php
                |--autoload_namespaces.php
                |--autoload_psr4.php
                |--autoload_real.php
                |--autoload_static.php
                |--ClassLoader.php
                |--installed.json
                |--LICENSE

## source 和 dist

```
$ composer info --all slim/slim
name     : slim/slim
descrip. : Slim is a PHP micro framework that helps you quickly write simple yet powerful web applications and APIs
keywords : framework, api, micro, router
versions : 4.x-dev, 3.x-dev, 3.8.1, 3.8.0, 3.7.0, 3.6.0, 3.5.0, 3.4.2, 3.4.1, 3.4.0, 3.3.0, 3.2.2, 3.2.1, 3.2.0, 3.1.0, 3.0.0, 3.0.0-RC3, 3.0.0-RC2, 3.0.0-RC1, 3.0.0-beta2, 3.0-beta1, 2.x-dev, 2.6.3, 2.6.2, 2.6.1, 2.6.0, 2.5.0, 2.4.3, 2.4.2, 2.4.1, 2.4.0, 2.3.5, 2.3.4, 2.3.3, 2.3.2, 2.3.1, 2.3.0, 2.2.0, 2.1.0, 2.0.0, 1.6.7, 1.6.6, 1.6.5, 1.6.4, 1.6.3, 1.6.2, 1.6.1, 1.6.0
type     : library
license  : MIT License (MIT) (OSI approved) https://spdx.org/licenses/MIT.html#licenseText
source   : [git] https://github.com/slimphp/Slim.git 6f765adb5dad996b586ecf6e362bb2ef7ae64ea4
dist     : [zip] https://files.phpcomposer.com/files/slimphp/Slim/6f765adb5dad996b586ecf6e362bb2ef7ae64ea4.zip 6f765adb5dad996b586ecf6e362bb2ef7ae64ea4
names    : slim/slim, psr/http-message-implementation

$ composer install --prefer-source
$ composer install --prefer-dist
```
* --prefer-dist   distribution，强制从已经发行的稳定版本资源库安装，即使是 dev 版本
* --prefer-source 强制从开发中的源代码库安装，包含了 VCS 信息我们中的许多人已然忘却

## 涉及到的包

monolog/monolog

mustache/mustache

slim/slim

phpunit

laravel/laravel

laracasts/Laravel-5-Generators-Extended

barryvdh/laravel-debugbar

psr/log

## Q&amp;A

依赖包里的require不会递归下载？
