---
title: Linux - PHP-FPM
categories:
  - Linux
tags:
  - Linux
  - PHP-FPM
---

设置 PHP-FPM 显示 PHP 错误
```bash
$ sudo vi /etc/php-fpm.d/www.conf

    php_flag[display_errors] = on

$ sudo systemctl reload nginx   # systemd
$ sudo service nginx reload     # sysvinit
```

默认 php.ini 的设置是显示错误的，可以确认下
```bash
$ sudo vi /etc/php.ini

    display_errors = On
```
