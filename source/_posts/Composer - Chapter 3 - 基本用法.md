---
title: Composer - Chapter 3 - 基本用法
categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer
---

## composer.json 和 composer.lock

### 安装依赖包时

composer执行install时，将会先检查composer.lock锁文件是否存在：
如果不存在，Composer 将读取 composer.json ，并获取依赖包匹配版本约束的最新版本放入 vendor 目录，然后创建锁文件，最后把所安装库的**确切版本**号写入 composer.lock 文件。
如果存在，Composer 将忽略composer.json文件，而使用 composer.lock 中定义的**确切版本**下载依赖库。
这意味着 composer.lock 文件锁定了项目依赖库的特定版本。

基于上述结论，应该把composer.lock提交到版本库：
可以保证团队的任何人员建立项目时，都会下载使用完全相同的依赖，从而减轻潜在错误对部署的影响。
对于独立开发人员，也可以保证在很长时间以后重新部署项目时，所使用的依赖库没有一点儿改变，即使依赖库已经有了更新的版本。

简单来讲，因为不会把依赖包都提交到版本库，为保证开发环境的一致性稳定性，使用 composer.lock 来锁死依赖包。 

 ???this lock file will not have any effect on other projects that depend on it. It only has an effect on the main project.
 ???锁文件只对主项目起作用，子项目里的锁文件对主项目不起作用。

### 更新依赖包时

需要更新依赖库的版本时，先修改 composer.json 文件中的版本定义，
这时如果运行 `composer install`，composer 会发现 composer.json 和 composer.lock 文件的版本不匹配，会警告：
```
Warning: 
The lock file is not up to date with the latest changes in composer.json.
You may be getting outdated dependencies.
Run update to update them.
Nothing to install or update
```
这时应该运行 `composer update` 命令，
composer 将会读取 composer.json 文件更新依赖的版本，然后把确切的版本号更新到 composer.lock 文件

## 定义依赖库

在自己的项目中使用 Composer 唯一需要的就是一个 `composer.json` 文件，描述了当前项目依赖的库和其他元数据。

### require 属性

在 composer.json 文件中设定 require 的值。告诉 Composer 项目需要依赖哪些包。

```
{
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```
* `1.0.*` 表示 >=1.0 <1.1
* 默认只查找 stable 版本的包，即`minimum-stability`的默认值
* 若设定查找 dev, alpha, beta, RC 版本时，则无法满足 composer 默认的最小稳定性要求，会抛出一个错误 ???有疑问好像也不是


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

### repositories 属性

安装依赖时查找包过程：
> 1.先使用包名进行查找，将优先从 repositories 属性注册的资源库中查找，没有找到则从默认的 Packagist 查找
> 2.根据包名找到后，再根据定义的版本约束查找最优匹配

```
"repositories": [
    {
        "type": "composer",
        "url": "http://packages.example.com"
    },
    {
        "type": "vcs",
        "url": "https://github.com/Seldaek/monolog"
    }
]
```

## 安装依赖库

### 根据 composer.json 安装依赖库

```
composer install
```

需要当前目录下有一个 composer.json

将创建一个 composer.lock 文件到你项目的根目录中

按惯例把第三方的代码到指定的目录 `vendor`。如果是 monolog 将会创建 `vendor/monolog/monolog` 目录。

Git 管理的项目，要添加 vendor 到 `.gitignore` 文件中。 否则所有的依赖库代码都将添加到版本库中。

### 直接命令行安装依赖库

```
$ composer require mustache/mustache
```

composer会自动更改 composer.json 和 composer.lock 文件，增加安装的新依赖库的信息

## 更新依赖库

composer.json 中定义版本约束时，很可能是一个范围，如 `1.*`，而 composer.lock 中肯定是写死的具体版本号，如`1.2.3`，虽然 `composer.lock` 保证了依赖包版本的稳定性，但也阻止了依赖包的更新升级，如依赖库有了`1.2.4`新版本，但`composer.lock`里仍然是`1.2.3`版本。

若需要升级依赖包，则使用`update`命令，`composer update` 相当于先删除 `composer.lock` 然后再运行 `composer install`，将读取 `composer.json` 的版本约束，获取符合版本约束的最新版本，再更新`composer.lock`写入确切的版本号

更新所有依赖库
```
$ composer update
```

更新特定依赖库
```
$ composer update monolog/monolog slim/slim
```

## autoload.php

composer在下载安装依赖库后，还在vendor目录下生成了一个 `autoload.php` 文件，用来自动加载库中的类，可以直接包含使用
```
require __DIR__ . '/vendor/autoload.php';

$log = new Monolog\Logger('name');
```

`autoload.php`文件会返回 autoloader 的实例，因此也可以将返回值存储在变量中再添加命名空间，测试自动加载类很有用
```
$loader = require __DIR__ . '/vendor/autoload.php';
$loader->addPsr4('Acme\\Test\\', __DIR__);
```

如果不想使用 Composer 提供的 autoloader，可以仅仅引入 `vendor/composer/autoload_*.php` 文件，它返回一个关联数组，你可以通过这个关联数组配置自己的 autoloader。

## autoload 属性

可以使用`autoload`属性让 `vendor/autoload.php` 自动加载自己的类文件
```javascript
{
    "autoload": {
        "psr-4": {"Acme\\": "src/"}
    }
}
```
* 为 Acme 命名空间注册一个 PSR-4 规范的 autoloader
* 定义了一个从命名空间到目录的映射，src 应在项目的根目录，与 vendor 文件夹同级。例如 src/Foo.php 文件应该包含 Acme\Foo 类。

添加 autoload 属性后，需要重新生成 vendor/autoload.php 文件
```
$ composer dump-autoload
Generating autoload files
```
