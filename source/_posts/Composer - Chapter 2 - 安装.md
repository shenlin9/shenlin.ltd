---
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
:   资源库的一种形式是源码库，composer将直接对接目标库的 VCS 获取源码进行安装，而你也必须安装目标库对应的VCS，常见VCS软件有：git,subversion,hg,fossil

## 安装

### 下载安装程序：

```
$ wget https://getcomposer.org/installer
```
或
```
$ php -r "readfile('https://getcomposer.org/installer');" | php
```
* 若 readfile 有错误提示，就使用 http 链接或者在 php.ini 中开启 php_openssl.dll

### 运行安装程序：

installer是php文件，将会检测php的设置和编译时的选项，如是否安装并启用openssl,zlib等，任何不兼容都会提出警告，因此不建议直接下载composer.phar

检测通过后将下载composer.phar文件到当前目录

```
$ php installer
```

也可以指定安装目录、文件名和版本号：

```
$ php installer --install-dir=bin --filename=composer  --version=1.0.0-alpha8
```

上面两步也可以这样一步执行

```
$ curl -sS https://getcomposer.org/installer | php --install-dir=bin --filename=composer
```

#### 程序里安装 Composer 

验证签名并安装
```
#!/bin/sh

EXPECTED_SIGNATURE=$(wget -q -O - https://composer.github.io/installer.sig)
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
ACTUAL_SIGNATURE=$(php -r "echo hash_file('SHA384', 'composer-setup.php');")

if [ "$EXPECTED_SIGNATURE" != "$ACTUAL_SIGNATURE" ]
then
    >&2 echo 'ERROR: Invalid installer signature'
    rm composer-setup.php
    exit 1
fi

php composer-setup.php --quiet
RESULT=$?
rm composer-setup.php
exit $RESULT
```
??? 为什么从官网下载的 installer 还要验证签名

### 运行composer

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
```
$ composer -V
Composer version 1.5.1 2017-08-09 16:07:22

$ composer
   ______
  / ____/___  ____ ___  ____  ____  ________  _____
 / /   / __ \/ __ `__ \/ __ \/ __ \/ ___/ _ \/ ___/
/ /___/ /_/ / / / / / / /_/ / /_/ (__  )  __/ /
\____/\____/_/ /_/ /_/ .___/\____/____/\___/_/
                    /_/
Composer version 1.5.1 2017-08-09 16:07:22

Usage:
  command [options] [arguments]

Options:
  -h, --help                     Display this help message
  -q, --quiet                    Do not output any message
  -V, --version                  Display this application version
      --ansi                     Force ANSI output
      --no-ansi                  Disable ANSI output
  -n, --no-interaction           Do not ask any interactive question
      --profile                  Display timing and memory usage information
      --no-plugins               Whether to disable plugins.
  -d, --working-dir=WORKING-DIR  If specified, use the given directory as working directory.
  -v|vv|vvv, --verbose           Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

Available commands:
  about           Shows the short information about Composer.
  archive         Creates an archive of this composer package.
  browse          Opens the package's repository URL or homepage in your browser.
  clear-cache     Clears composer's internal package cache.
  clearcache      Clears composer's internal package cache.
  config          Sets config options.
  create-project  Creates new project from a package into given directory.
  depends         Shows which packages cause the given package to be installed.
  diagnose        Diagnoses the system to identify common errors.
  dump-autoload   Dumps the autoloader.
  dumpautoload    Dumps the autoloader.
  exec            Executes a vendored binary/script.
  global          Allows running commands in the global composer dir ($COMPOSER_HOME).
  help            Displays help for a command
  home            Opens the package's repository URL or homepage in your browser.
  info            Shows information about packages.
  init            Creates a basic composer.json file in current directory.
  install         Installs the project dependencies from the composer.lock file if present, or falls back on the composer.json.
  licenses        Shows information about licenses of dependencies.
  list            Lists commands
  outdated        Shows a list of installed packages that have updates available, including their latest version.
  prohibits       Shows which packages prevent the given package from being installed.
  remove          Removes a package from the require or require-dev.
  require         Adds required packages to your composer.json and installs them.
  run-script      Runs the scripts defined in composer.json.
  search          Searches for packages.
  self-update     Updates composer.phar to the latest version.
  selfupdate      Updates composer.phar to the latest version.
  show            Shows information about packages.
  status          Shows a list of locally modified packages.
  suggests        Shows package suggestions.
  update          Updates your dependencies to the latest version according to composer.json, and updates the composer.lock file.
  validate        Validates a composer.json and composer.lock.
  why             Shows which packages cause the given package to be installed.
  why-not         Shows which packages prevent the given package from being installed.
```
