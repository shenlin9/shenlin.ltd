title: Composer - Chapter 6 - Composer.json Schema
categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer

---

主要介绍在 `composer.json` 文件中可用的字段属性。

<!--more-->

## JSON Schema

`composer.json` 文件首先是一个 JSON 格式的文件，因此要符合JSON Schema 规范 ：http://json-schema.org/

实际上，`composer validate` 命令验证 `composer.json` 文件时使用的就是一个 JSON Schema : https://getcomposer.org/schema.json 

## 属性

### name

格式: vendor/project

不区分大小写，可以使用任何字符，包括空格

为方便搜索安装，建议使用小写、较短的名字，且只使用字母数字组合

资源包必须

## description

包的简单描述，通常只有一行长。

资源包必须

## version

多数情况下都应该省略

## type

定义包的类型，包的类型决定了包的安装逻辑，如果你的包需要特殊的安装逻辑，也可以自定义类型

支持 4 种类型：library, project, metapackage, composer-plugin

默认为 library，建议省略 type 属性定义，使用默认就好

### library

被其他程序调用才可以运行的公共代码，安装时只是简单的把文件复制到 vendor 目录

### project

就是一个完整的程序以包的形式发布了，如 symfony 标准版

### metapackage

不包含任何文件的空包，不向文件系统写入任何东西，包括的是依赖并且会触发依赖包的安装

### composer-plugin

自定义类型包的安装程序

## keywords

可选数组，可用于搜索和过滤

## homepage

项目的URL

## time

版本的发布时间，格式必须为`YYYY-MM-DD` 或 `YYYY-MM-DD HH:MM:SS`

## license

可选，但强烈推荐使用

可为字符串或数组
```
{
    "license": "MIT"
}

//多个许可
{
    "license": [
       "LGPL-2.1",
       "GPL-3.0+"
    ]
}

//或者使用 or 写法
{
    "license": "(LGPL-2.1 or GPL-3.0+)"
}
```

闭源软件 :
```
{
    "license": "proprietary"
}
```

常见许可 :

    Apache-2.0
    BSD-2-Clause
    BSD-3-Clause
    BSD-4-Clause
    GPL-2.0
    GPL-3.0
    LGPL-2.1
    LGPL-3.0
    MIT

更多许可列表：https://spdx.org/licenses/

## authors

可选，但强烈推荐使用

对象数组，每位作者包含四个字段：name,email,homepage,role

```
{
    "authors": [
        {
            "name": "Nils Adermann",
            "email": "naderman@naderman.de",
            "homepage": "http://www.naderman.de",
            "role": "Developer"
        },
        {
            "name": "Jordi Boggiano",
            "email": "j.boggiano@seld.be",
            "homepage": "http://seld.be",
            "role": "Developer"
        }
    ]
}
```

## support

怎样获得项目帮助支持，包括：email, issues, forum, wiki, irc, source, docs, rss

```
{
    "support": {
        "email": "support@example.org",
        "irc": "irc://irc.freenode.org/composer"
    }
}
```

## Package links

下面的所有属性都是可选的，都是映射到一个对象，对象元素是包名到版本约束的映射，只有`suggest`例外，是包名到功能描述的映射。

### require 和 require-dev (root-only)

`require` 列出当前包需要的包

`require-dev` 列出开发或测试时才需要使用的包，注意默认是安装的，但 `install` 和 `update` 都支持`--no-dev`选项

#### 稳定性标记

`require` 和 `require-dev` 支持稳定性标记，

### conflict

列出所有和当前包有冲突的包，这些儿会产生冲突的包将不允许和当前包一起安装。

注意版本约束的写法，如 `<1.0 >=1.1` 的逻辑操作表示 and，正确的写法应为 `<1.0 || >=1.1`

### replace

列出所有可以被当前包替换的包。

两个应用场景：

1.可以 fork 一个包，使用`replace`属性标明可以替换掉原包，修改后用不同的名字和版本号发布，则当其它包安装了你的包，再请求原包时，将仍然使用你的包，而不会安装原包，因为你的包替换了原包。

2.包含了子包的包，如 Symfony 的每个组件都是可独立安装的包，而 `symfony/symfony` 的 `replace` 属性包含了 Symfony 的所有组件，则如果安装了 `symfony/symfony` 包，也将满足对各个独立组件的依赖。

### provide

当前包还包含的其他包，对于公共接口来说很有用，比如当前包依赖于一个虚拟包`logger`，则任何实现了`logger`的包都应列表在这里

### suggest

安装当前包完毕后，会显示一个提示信息，告诉用户还推荐安装哪些儿包，以及这些儿包的功能，这些推荐的包都不是必须的，但可提供额外增强的功能

```
{
    "suggest": {
        "monolog/monolog": "Allows more advanced logging of the application flow",
        "ext-xml": "Needed to support XML format in class Foo"
    }
}
```

## autoload

4 种自动加载的方式：PSR-4, PSR-0, Classmap, Files

推荐使用 PSR-4，在添加了新的 class 后，无需重新生成 autoloader

### PSR-4

### PSR-0

### Classmap

### Files

### Exclude files from classmaps

### Optimizing the autoloader

## autoload-dev (root-only)

定义了开发环境的自动加载，为了不影响生产环境的自动加载，所以要有专用的路径和配置

```
{
    "autoload": {
        "psr-4": { "MyLibrary\\": "src/" }
    },
    "autoload-dev": {
        "psr-4": { "MyLibrary\\Tests\\": "tests/" }
    }
}
```

## include-path

不赞成使用此属性

新代码应该使用自动加载

## target-dir

不赞成使用此属性

是针对 PSR-0 规范的自动加载，但现在应该都迁移到 PSR-4，PSR-4 没有这个属性

## minimum-stability (root-only)

定义了过滤包时的稳定性约束，默认值`stable`，可选的值有：dev, alpha, beta, RC, stable

针对的是当前项目，还可以针对个别包定义稳定性，通过在版本约束后面添加稳定性标记，如`1.0@beta`

## prefer-stable (root-only)

`"prefer-stable": true`

当兼容的 stable 版本可用时，总是优先使用

## repositories (root-only)

资源库不会递归执行，只有主项目 `composer.json` 文件中的资源库才会被搜索，依赖包的 `composer.json` 文件里注册的资源库会被忽略。

支持 4 种类型的资源库：composer, vcs, pear, package

**composer**
是一个通过网络访问的 `packages.json` 文件，此文件的内容是一个 `composer.json` 文件的列表，访问时使用 PHP stream，可以通过 `options` 添加额外的选项
```
{
    "repositories": [
        {
            "type": "composer",
            "url": "https://packages.example.com",
            "options": {
                "ssl": {
                    "verify_peer": "true"
                }
            }
        }
    ]
}
```

**vcs**
从 git,svn 等版本库获取资源包
```
{
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/Seldaek/monolog"
        }
    ]
}
```

**pear**
从 pear 库导入资源包
```
{
    "repositories": [
        {
            "type": "pear",
            "url": "https://pear2.php.net"
        }
    ]
}
```

**package**
如果依赖的项目对 Composer 没有任何支持，则使用内联包

```
{
    "repositories": [
        {
            "type": "package",
            "package": {
                "name": "smarty/smarty",
                "version": "3.1.7",
                "dist": {
                    "url": "http://www.smarty.net/files/Smarty-3.1.7.zip",
                    "type": "zip"
                },
                "source": {
                    "url": "https://smarty-php.googlecode.com/svn/",
                    "type": "svn",
                    "reference": "tags/Smarty_3_1_7/distribution/"
                }
            }
        }
    ]
}
```

## config (root-only)

只针对 Project 项目类型

### process-timeout
### use-include-path
### preferred-install
### store-auths
### github-protocols
### github-oauth
### gitlab-oauth
### gitlab-token
### disable-tls
### secure-http
### bitbucket-oauth
### cafile
### capath
### http-basic
### platform
### vendor-dir
### bin-dir
### data-dir
### cache-dir
### cache-files-dir
### cache-repo-dir
### cache-vcs-dir
### cache-files-ttl
### cache-files-maxsize
### bin-compat
### prepend-autoloader
### autoloader-suffix
### optimize-autoloader
### sort-packages
### classmap-authoritative
### apcu-autoloader
### github-domains
### github-expose-hostname
### gitlab-domains

gitlab 服务器的域名列表，默认为 `["gitlab.com"]`

### notify-on-install

资源库可以定义一个通知 URL，当资源库里的依赖包被安装时，资源库就会收到通知

默认为 true

### discard-changes

在非互动模式下，处理 dirty update 时的默认行为，有 true, false, stash 三个值，默认 false

true 表示放弃修改 vendor 目录的修改，

stash 表示存起来并尝试重新应用

### archive-format

归档格式，默认为 tar，还可为 zip

### archive-dir

设置归档目录，默认为当前工作目录

### htaccess-protect

默认为 true，设置为 false 则不创建 `.htaccess` 文件

## scripts (root-only)

通过脚本参与到 Composer 安装包的各个环节

具体参考独立章节：scripts

## extra

为上面的 `scripts` 属性准备的任意的额外数据，

在一个脚本文件的事件处理程序获取方法：
```
$extra = $event->getComposer()->getPackage()->getExtra();
```

## bin

应该被当做二进制文件对待，且应该通过符号链接安装到 `vendor/bin` 目录的文件

具体参考独立章节：vendor bianries

## archive

包括了一些儿包归档时的选项集，主要是 `exclude` 选项，表示归档时排除的路径，语法规则和`.gitignore`文件一样
```
{
    "archive": {
        "exclude": ["/foo/bar", "baz", "/*.test", "!/foo/bar/baz"]
    }
}
```

## non-feature-branches

是一个字符串数组

当 Composer 遇到看起来不像版本号的非数字分支名时（如 `latest`, `current` 等），将会按照特性分支处理：去找它的父分支中看起来像版本号或者是名称特殊的分支（如 master），然后使用找到的父分支版本号或父分支的特殊名称当做这个分支的版本号。

这个属性的设置即是为了更改上述默认处理规则，可以设置分支名的正则，匹配正则的分支都会被作为 `dev` 版本的分支。

如有测试分支 `latest-testing`，不设置时默认规则显示版本：
```
$ composer show -s
versions : * dev-master
```
增加设置
```
{
    "non-feature-branches": ["latest-.*"]
}
```
显示版本
```
$ composer show -s
versions : * dev-latest-testing
```
