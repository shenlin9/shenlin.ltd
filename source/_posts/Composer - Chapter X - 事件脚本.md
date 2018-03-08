---
title: Composer - Chapter 5 - 事件脚本
categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer
---

root 包 composer.json 文件里的 root JSON 对象属性：scripts，可以定义一系列触发事件和对应的事件脚本，格式如下:
```
{
    "scripts": {
        "事件名A": "事件脚本1",
        "事件名B": ["事件脚本2","事件脚本3"]
    }
}
```

也只有 root 包 composer.json 文件里定义的 scripts 才会执行，依赖包里定义的脚本不会被执行。

<!--more-->

## 事件脚本

### 执行 shell 命令

```
{
    "scripts":{
        "post-autoload-dump":  "phpunit -c app/"
    }
}
```

### 执行 PHP 类静态方法

```
{
    "scripts":{
        "post-package-install": "MyVendor\\MyClass::postPackageInstall"
    }
}
```
* 当然前提是确保 PHP 类文件存放的位置能被 Composer 自动载入，则`autoload`属性必须是 psr-0、psr-4、classmap 定义的。
* 当事件触发时，被调用的 PHP 静态方法会收到一个参数，一个 Composer\EventDispatcher\Event 对象，对象有 getName() 方法可以获取事件名称，getComposer() 方法获取当前Composer实例，getIO()方法获取当前的输入输出流。


单条脚本可以定义为字符串，多条脚本必须定义为数组，每个数组元素包含一条脚本；
```
{
    "scripts":{
        "post-install-cmd": [
            "MyVendor\\MyClass::warmCache",
            "phpunit -c app/"
        ]
    }
}
```

### 执行自定义命令

composer 预定义了可触发的事件名列表，如果写的事件名不在列表中，则表示自定义命令，例如：

```
{
    "scripts":{
        "pre-install-cmd":"echo 'install will begin now...'",
        "custom-cmd":"echo 'this is a custom define cmd...'"
    }
}
```

pre-install-cmd 是系统预定义事件名，会被系统自动触发；
custom-cmd 则是用户自定义命令，不会被系统自动触发，但可以像执行 composer 内容命令一样运行：

```
$ composer custom-cmd
```

### 使用 run-script

运行命令
```
$ composer run-script pre-install-cmd
$ composer run-script custom-cmd
```

查看用户自定义命令列表
```
$ composer run-script -l
```

传递参数
```
$ composer run-script post-install-cmd -- --check
```
* 参数作为数组传递给了 CLI 处理程序，通过 `$event->getArguments()` 可以读取


### 使用 @ 符号复用命令

#### 使用 @ 符号复用自定义命令
```
{
    "scripts":{
        "test": [
            "@clearCache",
            "phpunit"
        ],
        "clearCache": "rm -rf cache/*" 
    }
}
```

#### 使用 @ 符号运行 composer 命令

```
{
    "scripts":{
        "test": [
            "@composer show",
            "phpunit"
        ],
    }
}
```

#### 使用 @ 符号运行 php 脚本
```
{
    "scripts":{
        "test":  "@php script.php"
    }
}
```

使用 @ 符号后，多个命令必须写到数组中，不能在字符串中执行多个命令，例如：
```
{
    "scripts":{
        "test1":  "echo 1",
        "test2":  "echo 2",
        "test3":  "@test1;@test2",
        "test4":  "@test1 && @test2"
    }
}
```
* test3 和 test4 都不可以

>  Question:
> 
>  php 脚本和 composer 命令不使用 @ 符号也可以运行，这样有什么区别？

## 触发事件

pre 前缀表示事件发生之前
post 前缀表示事件发生之后

### Command Events


|事件名|触发时间|
|-|-|
|pre-install-cmd|存在 lock 文件 install 命令执行之前触发|
|post-install-cmd|存在 lock 文件 install 命令执行之后触发|
|pre-update-cmd|update 命令执行之前<br/><br/>或不存在 lock 文件 install 命令执行之前触发|
|post-update-cmd|update 命令执行之后<br/><br/>或不存在 lock 文件 install 命令执行之后触发|
|post-status-cmd|status 命令执行之后触发|
|pre-archive-cmd|archive 命令执行之前触发|
|post-archive-cmd|archive 命令执行之后触发|
|pre-autoload-dump|autoloade.php被更新前触发<br/><br/>无论是哪个命令导致的更新：install/update/dump-autoload|
|post-autoload-dump|autoloade.php被更新后触发<br/><br/>无论是哪个命令导致的更新：install/update/dump-autoload|
|post-create-project-cmd|create-project 命令执行后触发|
|post-root-package-install|执行 create-project 命令创建项目并安装 root 包，root 包安装完成后触发|
 

### Installer Events

|事件名|触发时间|
|-|-|
|pre-dependencies-solving|依赖解决前触发|
|post-dependencies-solving|依赖解决后触发|

### Package Events

|事件名|触发时间|
|-|-|
|pre-package-install|资源包安装前触发|
|post-package-install|资源包安装后触发|
|pre-package-update|资源包更新前触发|
|post-package-update|资源包更新后触发|
|pre-package-uninstall|资源包卸载前触发|
|post-package-uninstall|资源包卸载后触发|

### Plugin Events

|事件名|触发时间|
|-|-|
|init|Composer实例初始化完成后触发|
|command|通过命令行界面执行任何 Composer 命令前触发<br/><br/>可以让你访问输入输出对象<br/><br/>occurs before any Composer Command is executed on the CLI.<br/> It provides you with access to the input and output objects of the program.|
|pre-file-download|文件下载完成前触发，<br/><br/>occurs before files are downloaded and allows you to manipulate the RemoteFilesystem object prior to downloading files based on the URL to be downloaded.|
