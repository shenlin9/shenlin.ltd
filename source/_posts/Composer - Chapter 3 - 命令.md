title: Composer - Chapter 3 - 命令
categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer

---

Composer 常用命令简介。

```
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
  init            Creates a basic composer.json file in current directory.
  install         Installs the project dependencies from the composer.lock file if present, or falls back on the composer.json.
  licenses        Shows information about licenses of dependencies.
  list            Lists commands
  outdated        Shows a list of installed packages that have updates available, including their latest version.
  prohibits       Shows which packages prevent the given package from being installed.
  remove          Removes a package from the require or require-dev.
  require         Adds required packages to your composer.json and installs them.
  run-script      Runs the scripts defined in composer.json.
  self-update     Updates composer.phar to the latest version.
  selfupdate      Updates composer.phar to the latest version.

  info            Shows information about packages.
  show            Shows information about packages.
  search          Searches for packages.

  status          Shows a list of locally modified packages.
  suggests        Shows package suggestions.
  update          Updates your dependencies to the latest version according to composer.json, and updates the composer.lock file.
  validate        Validates a composer.json and composer.lock.
  why             Shows which packages cause the given package to be installed.
  why-not         Shows which packages prevent the given package from being installed.
```
    
<!--more-->

## composer command -h

显示帮助，如
```
composer show -h
```

## composer init

以向导的方式在当前目录初始化一个 composer.json 文件，包括：
* 定义你当前项目的名称、描述、作者、类型（library,project,metapackage,composer-plugin）
* 定义你当前项目的依赖包、依赖的开发包

```
$ composer init


  Welcome to the Composer config generator



This command will guide you through creating your composer.json config.

Package name (<vendor>/<name>) [ssl/gitgo.git]: shenlin/gitgo
Description []: a git and php composer test
Author [shenlin9 <bitbite@foxmail.com>, n to skip]:
Minimum Stability []: stable
Package Type (e.g. library, project, metapackage, composer-plugin) []: library
License []: MIT

Define your dependencies.

Would you like to define your dependencies (require) interactively [yes]? y
Search for a package: monolog/monolog
Enter the version constraint to require (or leave blank to use the latest version):
Using version ^1.23 for monolog/monolog
Search for a package:
Would you like to define your dev dependencies (require-dev) interactively [yes]? y
Search for a package: slim/slim
Enter the version constraint to require (or leave blank to use the latest version):
Using version ^3.8 for slim/slim
Search for a package:

{
    "name": "shenlin/gitgo",
    "description": "a git and php composer test",
    "type": "library",
    "require": {
        "monolog/monolog": "^1.23"
    },
    "require-dev": {
        "slim/slim": "^3.8"
    },
    "license": "MIT",
    "authors": [
        {
            "name": "shenlin9",
            "email": "bitbite@foxmail.com"
        }
    ],
    "minimum-stability": "stable"
}

Do you confirm generation [yes]? y
Would you like the vendor directory added to your .gitignore [yes]?bcit-ci/codeigniter y

$ cat .gitignore
/vendor/
```

## composer search

search 可以模糊查找，info 和 show 只能严格按照 `vendor/pakcage` 的格式写

## composer info

是 show 的别名

## composer show

`composer show [option] [vendor/package]`

默认只显示本地已安装包

不加 --all 选项，则必须在 composer.json
所在目录执行，因为这时只显示本地已安装包的信息，要读取 composer.json 文件

加上 --all 选项，则先查找当前目录下有无 composer.json 文件，
有则使用 composer.json 文件里定义的 repository 去联网查找包信息
没有 composer.json 文件则从默认注册的 packagist 站点联网查找包信息。


列出本地已安装的包
```
composer show
```

联网查找列出所有可安装包
```
composer show --all
```

列出本地已安装的monolog包的信息，版本号只显示当前版本
```
composer show monolog/monolog
```

会联网到 repository 去查找
```
composer show --all monolog/monolog
```
* 输出结果和不加选项 --all 的唯一不同之处是版本号会显示所有的版本


### composer create-project [vendor/package] [install path] [which version]

默认创建 vendor/package 中的 package 文件夹
```
composer create-project laravel/laravel
```

指定安装文件夹 和 要安装的版本号
```
composer create-project slim/slim slim-2.6 2.6.2
```

https://zhuanlan.zhihu.com/p/27943241
