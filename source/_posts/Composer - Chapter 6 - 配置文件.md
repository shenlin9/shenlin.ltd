title: Composer - Chapter 6 - 配置文件
categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer

---

## 目录结构

全局配置文件
config.json

局部配置文件
composer.json

单独的身份认证文件
auth.json

composer.lock
/vendor

## 定义依赖库

### require 属性

在 composer.json 文件中指定 require key 的值。告诉 Composer 项目需要依赖哪些包。

    {
        "require": {
            "monolog/monolog": "1.0.*"
        }
    }

### require-dev 属性和 --no-dev 选项

一些儿开发中需要用到的包，可能在生产环境中无需部署，例如调试包，这些儿包可以在 `composer.json` 文件中用属性 `require-dev` 指明，

```
"require-dev": {
    "fzaninotto/faker": "~1.4",
    "mockery/mockery": "0.9.*",
    "phpunit/phpunit": "~5.7"
}
```

在部署到生产环境中时，配合`--no-dev`指明不部署这些儿包即可。
```
$ composer install --no-dev
```

## 安装依赖库

### 根据 composer.json 安装依赖库

```
composer install
```

需要当前目录下有一个 composer.json

install 命令将创建一个 composer.lock 文件到你项目的根目录中

按惯例把第三方的代码到指定的目录 vendor。如果是 monolog 将会创建 vendor/monolog/monolog 目录。

Git 管理的项目，要添加 vendor 到 .gitignore 文件中。 否则所有的依赖库代码都将添加到版本库中。

### 直接命令行安装依赖库

```
$ composer require mustache/mustache
```

composer会自动更改 composer.json 和 composer.lock 文件，增加安装的新依赖库的信息

## 自动加载安装的库

Composer 还准备了一个自动加载文件，它可以加载 Composer 下载的库中所有的类文件。使用它，你只需要将下面这行代码添加到你项目的引导文件中：

```
require 'vendor/autoload.php';
```

## composer.json 和 composer.lock

### 安装依赖包时

composer执行install时，将会先检查composer.lock锁文件是否存在，
如果不存在，Composer 将读取 composer.json 并创建锁文件，最后把所安装库的确切版本号写入 composer.lock 文件。
如果存在，Composer 将忽略composer.json文件，而使用 composer.lock 中定义的确切版本下载依赖库。
这意味着 composer.lock 文件锁定了项目依赖库的特定版本。

基于上述结论，应该把composer.lock提交到版本库，
可以保证团队的任何人员建立项目时，都会下载使用完全相同的依赖，从而减轻潜在错误对部署的影响。
对于独立开发人员，也可以保证在很长时间以后重新部署项目时，所使用的依赖库没有一点儿改变，即使依赖库已经有了更新的版本。

其实就是因为不会把依赖包都提交到版本库，为保证开发环境的一致性稳定性，使用
composer.lock 来锁死依赖包。 

### 更新依赖包时

需要更新依赖库的版本时，先修改 composer.json 文件中的版本定义，
这时如果运行 `composer install`，composer 会发现 composer.json 和 composer.lock 文件的版本不匹配，会警告：
```
Warning: The lock file is not up to date with the latest changes in composer.json. You may be getting outdated dependencies. Run update to update them.
Nothing to install or update
```
这时应该运行 `composer update` 命令，
composer 将会读取 composer.json 文件更新依赖的版本，然后把确切的版本号更新到 composer.lock 文件

### 依赖包初始化时

项目下没有依赖库，有两种可能；
一是还没运行过composer
二是项目代码是从版本库里下载的，只有composer.lock和composer.json文件
这时应该运行composer install，会优先读取 composer.lock 文件，

项目下有依赖库，需要对依赖库进行版本升级时，
应该运行composer update，读取 composer.json 文件新的版本定义并更新 composer.lock 文件


## autoloader

composer在下载安装依赖库后， 还在vendor目录下生成了一个 `autoload.php` 文件，用来自动加载库中的类。

```php
require 'vendor/autoload.php';
```

如果不想使用 Composer 提供的 autoloader，可以仅仅引入 `vendor/composer/autoload_*.php` 文件，它返回一个关联数组，你可以通过这个关联数组配置自己的 autoloader。

### autoload 字段

可以在 composer.json 的 autoload 字段中增加自己的 autoloader。

```javascript
{
    "autoload": {
        "psr-4": {"Acme\\": "src/"}
    }
}
```

添加 autoload 字段后，需要再次运行 install 命令来生成 vendor/autoload.php 文件。

Composer 将注册一个 PSR-4 autoloader 到 Acme 命名空间。

可以定义一个从命名空间到目录的映射。此时 src 应在你项目的根目录，与 vendor 文件夹同级。例如 src/Foo.php 文件应该包含 Acme\Foo 类。

引用这个文件也将返回 autoloader 的实例，你可以将包含调用的返回值存储在变量中，并添加更多的命名空间。
这对于在一个测试套件中自动加载类文件是非常有用的，例如

```php
$loader = require 'vendor/autoload.php';
$loader->add('Acme\\Test\\', __DIR__);
```

除了 PSR-4 自动加载，classmap 也是支持的。这允许类被自动加载，即使不符合 PSR-0 规范。


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




composer update 会先删除原来安装的版本，再安装 composer.json文件指定的版本


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
* --prefer-source 强制从开发中的源代码库安装，包含了 VCS 信息
    

