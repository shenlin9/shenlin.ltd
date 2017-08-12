title: Composer - Chapter X - Vendor binaries
categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer

---

一个 Composer 包中被用户使用的命令行脚本称为 vendor binary，用户不需要的如 build, compile 等命令行脚本则不是 vendor binary。

<!--more-->

## 定义 vendor binary

在 composer.json 文件里添加文件列表：作用是告诉 Composer 把这些脚本文件或二进制命令放到 `vendor/bin` 目录下
```
{
    "bin": ["bin/my-script", "bin/my-other-script"]
}
```

## 查看默认存放目录

当前项目
```
$ composer config bin-dir
vendor/bin

$ composer config bin-dir --absolute
D:\git\gitgo.git/vendor/bin
```

全局
```
$ composer global config bin-dir --absolute
Changed current directory to C:/Users/ssl/AppData/Roaming/Composer
C:\Users\ssl\AppData\Roaming\Composer/vendor/bin
```

## 更改默认存放目录

更改默认的 `vendor/bin` 目录
```
{
    "config": {
        "bin-dir": "scripts"
    }
}
```
还可以使用环境变量 `COMPOSER_BIN_DIR` 更改

## 对项目的影响

主项目的 `composer.json` 文件里定义了 vendor binary，对主项目来说没什么作用

主项目的依赖包定义了 vendor binary，则主项目在安装该依赖包时，会创建列表的脚本文件或二进制文件的符号链接(类unix系统)到 `bin/vendor` 目录

如果主项目是 project-a，`composer.json` 里定义如下
```
{
    "name": "my-vendor/project-a",
    "bin": ["bin/project-a-bin"]
}
```
则运行`composer install`时`bin`属性对项目毫无影响

如果主项目是 project-b，且依赖于 project-a，
```
{
    "name": "my-vendor/project-b",
    "require": {
        "my-vendor/project-a": "*"
    }
}
```
则运行`composer install`时，
将根据属性`bin`的设置创建符号链接 `/vendor/bin/project-a-bin` 链接 `vendor/my-vendor/project-a/bin/project-a-bin`

## Windows 兼容

被 Composer 管理的包本身无须为兼容 Windows 包含 `.bat` 文件，在 Windows 环境下安装 Composer 包时，Composer 使用下面的方法进行自动处理：

1. 创建和二进制文件同名的 `.bat` 文件来引用二进制文件，应用于 Windows 命令行窗口
2. 创建和二进制文件同名的 Unix 样式的代理文件，应用于 Windows 下的类 Unix 环境如 cygwin 和 gitbash
