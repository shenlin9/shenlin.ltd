title: Composer - Chapter 5 - 环境变量
categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer

---

## COMPOSER 

设置 `composer.json` 为其他文件名，对应的锁文件也将自动更改

## COMPOSER_HOME

COMPOSER_HOME 是一个**全局的**隐藏的**多项目共享**的目录 : windows 下为：`$HOME\AppData\Roaming\Composer` linux 下为 `~/.Composer`

COMPOSER_HOME 下的子目录 `vendor/bin` 在安装 Composer 时应该是已经添加到了 PATH 环境变量

# COMPOSER_HOME/config.json

全局配置文件

配置项会被项目配置文件 `composer.json` 中同名的配置项覆盖

## COMPOSER_VENDOR_DIR

更改默认的依赖包 `vendor` 目录

## COMPOSER_BIN_DIR

更改默认的依赖包二进制文件 `vendor/bin` 目录

也可使用属性 `bin-dir` 更改
```
{
    "config": {
        "bin-dir": "scripts"
    }
}
```

## COMPOSER_CACHE_DIR

linux 默认路径： `$COMPOSER_HOME/cache`
windows 默认路径 ：`%LOCALAPPDATA%/Composer`

也可以使用属性 `cache-dir` 来设置

## COMPOSER_ALLOW_SUPERUSER

设置为 1，则在使用超级用户 root 执行命令时不发出警告

## COMPOSER_PROCESS_TIMEOUT

Composer 等待命令执行完成的时间，默认为 300 秒

## COMPOSER_HTACCESS_PROTECT

默认值 1，设为 0 则 Composer 不在 COMPOSER_HOME 、COMPOSER_CACHE_DIR 和数据目录创建 `.htaccess` 文件

## COMPOSER_CAFILE

设置包含 SSL/TLS 验证文件的路径

