title: Composer - Chapter 4 - 命令
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
  help            Displays help for a command

  global          Allows running commands in the global composer dir ($COMPOSER_HOME).
  list            Lists commands
  diagnose        Diagnoses the system to identify common errors.
  self-update     Updates composer.phar to the latest version.
  selfupdate      Updates composer.phar to the latest version.
  suggests        Shows package suggestions.
  why             Shows which packages cause the given package to be installed.
  why-not         Shows which packages prevent the given package from being installed.

  validate        Validates a composer.json and composer.lock.
  config          Sets config options.

  archive         Creates an archive of this composer package.
  browse          Opens the package's repository URL or homepage in your browser.
  clear-cache     Clears composer's internal package cache.
  clearcache      Clears composer's internal package cache.
  create-project  Creates new project from a package into given directory.
  depends         Shows which packages cause the given package to be installed.
  dump-autoload   Dumps the autoloader.
  dumpautoload    Dumps the autoloader.
  exec            Executes a vendored binary/script.
  home            Opens the package's repository URL or homepage in your browser.
  licenses        Shows information about licenses of dependencies.
  outdated        Shows a list of installed packages that have updates available, including their latest version.
  prohibits       Shows which packages prevent the given package from being installed.

  init            Creates a basic composer.json file in current directory.
  install         Installs the project dependencies from the composer.lock file if present, or falls back on the composer.json.
  require         Adds required packages to your composer.json and installs them.
  update          Updates your dependencies to the latest version according to composer.json, and updates the composer.lock file.
  remove          Removes a package from the require or require-dev.
  run-script      Runs the scripts defined in composer.json.

  info            Shows information about packages.
  show            Shows information about packages.
  search          Searches for packages.
  status          Shows a list of locally modified packages.
```
    
<!--more-->

## help

显示帮助，如
```
composer show -h
composer show --help
composer help show
```

## global 

COMPOSER_HOME 是一个**全局的**隐藏的**多项目共享**的目录 : windows 下为：`$HOME\AppData\Roaming\Composer` linux 下为 `~/.Composer`

COMPOSER_HOME 下的子目录 `vendor/bin` 在安装 Composer 时应该是已经添加到了 PATH 环境变量

查看全局 bin-dir 路径
```
$ composer global config bin-dir --absolute
Changed current directory to C:/Users/ssl/AppData/Roaming/Composer
C:\Users\ssl\AppData\Roaming\Composer/vendor/bin
```

global 使得 Composer 运行命令时临时切换到 COMPOSER_HOME 目录，因此其`bin`属性定义的命令会自动安装到 `COMPOSER_HOME/vendor/bin`
```
$ composer global require fabpot/php-cs-fixer
```
因此，执行上述命令后，`php-cs-fixer` 应是全局可用

更新全局命令
```
$ composer global update
```

## init

以向导的方式在当前目录初始化一个 composer.json 文件，包括：
* 定义你当前项目的名称、描述、作者、类型（library,project,metapackage,composer-plugin）
* 定义你当前项目的依赖包、依赖的开发包

search for a package 格式应为 `foo/bar:1.0.0`，不写版本号则取最新的稳定版本

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
## install

有 `composer.lock` 时则读取它，解决依赖关系，把确切的版本安装到 vendor 目录，

没有 `composer.lock` 时则读取 `composer.json`，解决依赖关系，创建 `composer.lock` 文件，把符合版本约束的最新版本安装到 vendor 目录，并把安装的确切版本号写入 `composer.lock`

`--prefer-dist`

对于稳定版本资源包，默认从 `dist` 属性定义的位置安装，为稳定的发行版

速度较快，还可以避免因为 Git 安装不当的可能出现的问题

`--prefer-source`

从 `dist` 属性定义的位置安装，为开发中的源码

可以用于修正一个bug，或者获取一个依赖包版本库的克隆

`--dev`

安装 `require-dev` 属性定义的依赖包，不安装则使用 `--no-dev`

`--dry-run`

模拟安装，不实际安装文件

`--no-autoloader`

不产生 `vendor/autoload.php` 文件

`--no-scripts`

不执行 `composer.json` 文件里定义的脚本

`--no-progress`

不显示进度，显示进度有时会干扰终端，或者脚本无法处理

`--no-suggest`

## update

支持通配符

```
$ composer update slim/*
```

## require

命令行直接安装依赖包，会把新安装的依赖包更新到 `composer.json` 文件，写入锁文件 `composer.lock`，重新生成autoload 文件

互动式安装
```
$ composer require
```

直接安装指定包
```
$ composer require phpunit/phpunit
Using version ^6.3 for phpunit/phpunit
./composer.json has been updated
...............
Writing lock file
Generating autoload files
```

## remove

```
$ composer remove slim/slim
```

## search

search 可以模糊查找，info 和 show 只能严格按照 `vendor/pakcage` 的格式写

## info

是 show 的别名

## show

`composer show [option] [vendor/package]`

默认只显示本地已安装包

不加 --all options，则必须在 composer.json
所在目录执行，因为这时只显示本地已安装包的信息，要读取 composer.json 文件

加上 --all options，则先查找当前目录下有无 composer.json 文件，
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
* 输出结果和不加options --all 的唯一不同之处是版本号会显示所有的版本

支持通配符
```
composer show --all monolog/*
```

### options

`--all `
列出已注册的资源库和默认资源库 packagist 中的所有包，默认只列出本地已安装的包

`--platform (-p)`
只列出平台包

`--self (-s)`
列出 root 包，也就是当前项目

`--latest (-l)`
列出本地已安装的包，并对应列出资源库中最新的版本

`--outdated (-o)`
隐含 `--latest` 参数，但只显示有新版本的本地安装包

`--available (-a)` List available packages only.
`--name-only (-N)` List package names only.
`--path (-P)` List package paths.
`--tree (-t)` List your dependencies as a tree. If you pass a package name it will show the dependency tree for that package.
`--minor-only (-m)` Use with `--latest`. Only shows packages that have minor SemVer-compatible updates.
`--direct (-D)` Restricts the list of packages to your direct dependencies.
`--strict` Return a non-zero exit code when there are outdated packages.
`--format (-f)` Lets you pick between text (default) or json output format.

## outdated

`composer show -o` 的别名

不同的颜色表示不同的状态：
绿色(=) 依赖包是最新版本无需更新
红色(!) 根据语义化版本定义，依赖包有了和版本约束兼容的版本可以更新
黄色(~) 根据语义化版本定义，依赖包有了新版本但不向后兼容，如果更新可能需要调整系统

## home

browse 的别名

## browse

显示软件包的资源库 URL
```
$ composer browse -s slim/slim
https://github.com/slimphp/Slim.git
```

显示软件包的主页 URL
```
$ composer browse -sH slim/slim
https://slimframework.com
```

打开软件包的资源库 URL
```
$ composer browse slim/slim
```

打开软件包的主页 URL
```
$ composer browse -H slim/slim
```

### options

`--homepage (-H)`
显示或打开主页，默认是资源库

`--show (-s)`
只显示 URL 不使用浏览器打开

## suggests

根据当前已安装的包的集合，列出推荐安装的包
```
$ composer suggests
aws/aws-sdk-php
doctrine/couchdb
ext-amqp
ext-mongo
ext-soap
ext-uopz
ext-xdebug
graylog2/gelf-php
mongodb/mongodb
php-amqplib/php-amqplib
php-console/php-console
phpunit/php-invoker
rollbar/rollbar
ruflin/elastica
sentry/sentry
```

或者针对特定包列出推荐
```
$ composer suggests phpunit/phpunit
ext-xdebug
phpunit/php-invoker
```

**显示推荐原因**
```
$ composer suggests -v
monolog/monolog suggests:
 - aws/aws-sdk-php: Allow sending log messages to AWS services like DynamoDB
 - doctrine/couchdb: Allow sending log messages to a CouchDB server
 - ext-amqp: Allow sending log messages to an AMQP server (1.0+ required)
 - ext-mongo: Allow sending log messages to a MongoDB server
 - graylog2/gelf-php: Allow sending log messages to a GrayLog2 server
 - mongodb/mongodb: Allow sending log messages to a MongoDB server via PHP Driver
 - php-amqplib/php-amqplib: Allow sending log messages to an AMQP server using php-amqplib
 - php-console/php-console: Allow sending log messages to Google Chrome
 - rollbar/rollbar: Allow sending log messages to Rollbar
 - ruflin/elastica: Allow sending log messages to an Elastic Search server
 - sentry/sentry: Allow sending log messages to a Sentry server
```

### options

`-v --verbose`
显示推荐安装原因，隐含有参数`--by-package --by-suggestion`

`--by-package`
按包分组

`--by-suggestion`
只显示包含推荐原因的安装包

## depends

显示哪些儿包在依赖指定的安装包
```
$ composer depends slim/slim
shenlin/gitgo  dev-feature  requires (for development)  slim/slim (^3.8)
```

### options

`-r --recursive` 递归到 root 包
`-t --tree` 显示为树的形式，隐含有`-r`options

## prohibits

显示哪些儿包在禁止指定的安装包安装，被禁止的原因很可能是因为版本约束
```
$ composer prohibits symfony/symfony 3.1
laravel/framework v5.2.16 requires symfony/var-dumper (2.8.*|3.0.*)

$ composer prohibits slim/slim:4.0
shenlin/gitgo  dev-feature  requires (for development)  slim/slim (^3.8)
```
可用于查找依赖包不能升级的原因

还可用于平台包，如检测PHP是否可以升级到8.0版本
```
$ composer prohibits php:8.0
doctrine/instantiator               1.1.0   requires  php (^7.1)
phar-io/manifest                    1.0.1   requires  php (^5.6 || ^7.0)
phar-io/version                     1.0.1   requires  php (^5.6 || ^7.0)
phpdocumentor/type-resolver         0.3.0   requires  php (^5.5 || ^7.0)
phpspec/prophecy                    v1.7.0  requires  php (^5.3|^7.0)
phpunit/php-code-coverage           5.2.2   requires  php (^7.0)
phpunit/php-timer                   1.0.9   requires  php (^5.3.3 || ^7.0)
phpunit/php-token-stream            2.0.0   requires  php (^7.0)
phpunit/phpunit                     6.3.0   requires  php (^7.0)
phpunit/phpunit-mock-objects        4.0.4   requires  php (^7.0)
sebastian/code-unit-reverse-lookup  1.0.1   requires  php (^5.6 || ^7.0)
sebastian/comparator                2.0.2   requires  php (^7.0)
sebastian/diff                      2.0.1   requires  php (^7.0)
sebastian/environment               3.1.0   requires  php (^7.0)
sebastian/exporter                  3.1.0   requires  php (^7.0)
sebastian/global-state              2.0.0   requires  php (^7.0)
sebastian/object-enumerator         3.0.3   requires  php (^7.0)
sebastian/object-reflector          1.1.1   requires  php (^7.0)
sebastian/recursion-context         3.0.0   requires  php (^7.0)
theseer/tokenizer                   1.1.0   requires  php (^7.0)
webmozart/assert                    1.2.0   requires  php (^5.3.3 || ^7.0)
```

### options

`-r --recursive` 递归到 root 包
`-t --tree` 显示为树的形式，隐含有`-r`options

## validate

验证 `composer.json` 文件的有效性
```
$ composer validate
./composer.json is valid
```
在新增一个 tag 或提交`composer.json`前都应该验证下

### options

`--no-check-all`
使用无边界的版本约束时不要警告

`--no-check-lock`
有`composer.lock`文件但不是最新的时不产生错误

`--no-check-publish`
`composer.json`的配置有效但不适合发行到 Packagist 时不产生错误

`--with-dependencies`
同时验证依赖包的`composer.json`文件

`--strict`
有错误或警告时退出码非0

## status

如果依赖包是从 source 源代码库安装的，而且你会修改依赖包的代码，

显示是否有修改
```
$ composer status
```

显示修改的详细信息
```
$ composer status -v
```

## self-update

更新 composer 到最新版本

默认只获取 stable 版本
```
$ composer selfupdate
$ composer self-update
```

获取指定版本
```
$ composer self-update 1.0.0-alpha7
```

也可以获取 pre-release 版本
```
$ composer self-update --preview
```

或者获取 Composer 最后一次提交的快照
```
$ composer self-update --snapshot
```

## config

使用此命令操作全局配置文件`config.json`和当前项目配置文件`composer.json`

最简单的读取和设置
```
$ composer config name shenlin/gitgo

$ composer config name
shenlin/gitgo
```

section的读取设置
```
$ composer config repositories.foo vcs https://github.com/foo/bar
```

格式为：`"extra": { "foo": { "bar": "value" } }` 的设置
```
$ composer config extra.foo.bar value
```

多个属性可以使用 JSON
```
$ composer config repositories.foo '{"type": "vcs", "url": "http://svn.example.org/my-project/", "trunk-path": "master"}'
```

### options

`-l | --list`
列出当前生效的配置

`-g | --global`
只针对全局配置

`--absolute`
获取`*-dir`一类的属性时，把相对路径转换为绝对路径，如`bin-dir`

`--unset`
移除设置项

## create-project

从现有的包创建新项目

默认创建 vendor/package 中的 package 文件夹
```
composer create-project laravel/laravel
```

指定安装文件夹 和 要安装的版本号
```
composer create-project slim/slim slim-2.6 2.6.2
```

??? 这个和 init 有什么不同

## dump-autoload

重新生成自动加载文件`vendor/autoload.php`

自动加载的方案有：PSR-0,PSR-4,classmap，其中前两个开发中使用方便，但性能较差，最后一个性能较好，但开发使用不方便

```
$ composer dump-autoload -o
```

### options

`--optimize (-o)`
把低效率的PSR-0/4替换为classmap，对于大项目替换过很耗时间，所以目前还不是默认options，但推荐生产环境使用

`--no-scripts`

`--classmap-authoritative (-a)`
只从 classmap 加载类，隐式调用`--optimize`

`--no-dev`
忽略属性 `autoload-dev`的规则

## clear-cache

清空 Composer 缓存目录的所有文件 

```
$ composer clear-cache
Cache directory does not exist (cache-vcs-dir):
Clearing cache (cache-repo-dir): C:\Users\ssl\AppData\Local\Composer\repo
Clearing cache (cache-files-dir): C:\Users\ssl\AppData\Local\Composer\files
Clearing cache (cache-dir): C:\Users\ssl\AppData\Local\Composer
All caches cleared.
```
???缓存目录

## licenses

列出包的名字、版本、许可
```
Name: shenlin/gitgo
Version: dev-feature
Licenses: MIT
Dependencies:

Name                                 Version  License
container-interop/container-interop  1.2.0    MIT
doctrine/instantiator                1.1.0    MIT
monolog/monolog                      1.23.0   MIT
myclabs/deep-copy                    1.6.1    MIT
nikic/fast-route                     v1.2.0   BSD-3-Clause
phar-io/manifest                     1.0.1    BSD-3-Clause
phar-io/version                      1.0.1    BSD-3-Clause
phpdocumentor/reflection-common      1.0      MIT
phpdocumentor/reflection-docblock    3.2.2    MIT
phpdocumentor/type-resolver          0.3.0    MIT
phpspec/prophecy                     v1.7.0   MIT
phpunit/php-code-coverage            5.2.2    BSD-3-Clause
```

### options

`--format`
text 和 json，默认 text, json 适合程序读取

`--no-dev`

## run-script

执行

### options

`--timeout`
脚本超时时间，以秒为单位，0 表示永不超时

`--dev`
包含 dev 脚本

`--no-dev`
不包含 dev 脚本

`--list (-l)`
列出用户定义的脚本

## exec

执行`vendor/bin`目录下的脚本或命令

列表查看
```
$ composer exec -l
Available binaries:
- phpunit
```

### options

`-l | --list`

## diagnose

系统诊断
```
$ composer diagnose
Checking composer.json: OK
Checking platform settings: OK
Checking git settings: OK
Checking http connectivity to packagist: OK
Checking https connectivity to packagist: OK
Checking github.com rate limit: OK
Checking disk free space: OK
Checking pubkeys:
Tags Public Key Fingerprint: 57815BA2 7E54DC31 7ECC7CC5 573090D0  87719BA6 8F3BB723 4E5D42D0 84A14642
Dev Public Key Fingerprint: 4AC45767 E5EC2265 2F0C1167 CBBB8A2B  0C708369 153E328C AD90147D AFE50952
OK
Checking composer version: OK
```

## archive

将当前项目归档为一个文件
```
$ composer archive
Creating the archive into ".".
Created: ./shenlin-gitgo-e69d4c53a2f3ed96cda622586f83e07e78adfdbe-df2479.tar
```

归档指定包的指定版本，注意：必须指定版本
```
$ composer archive slim/slim 3.8.1
Searching for the specified package.
Found multiple matches, selected slim/slim 3.8.1.
Alternatives were slim/slim 3.8.1, slim/slim 3.8.1.
Please use a more specific constraint to pick a different package.
Creating the archive into ".".
  - Installing slim/slim (3.8.1): Loading from cache
Created: ./slim-slim-5385302707530b2bccee1769613ad769859b826d-zip-8aa1e0.tar
```

### options

`--format`
默认 `tar` 还可选 `zip`

`--dir`
归档文件保存目录，默认当前目录

`--file`
归档文件名

https://zhuanlan.zhihu.com/p/27943241
