---
title: PHP - CodeIgniter
categories:
  - PHP
  - CodeIgniter
tags:
  - CodeIgniter
---

## 建立站点目录

下载解押 CodeIgniter , 然后将 CodeIgniter 改名为 www，此目录将作为站点根目录

建立项目目录，如 `/home/shenlin/zaimusic` ，把 www 目录下的 system 和 application

子目录移出放入项目目录，再把将 www 目录也放入项目目录，最后的目录如下：

    /home/shenlin/zaimusic/wwww
    /home/shenlin/zaimusic/system
    /home/shenlin/zaimusic/application

## 更改目录设置

更改 www 目录下的 index.php

    $system_path = '/home/shenlin/zaimusic/system';

    $application_folder = '/home/shenlin/zaimusic/application';

更改 application 目录下的 config/config.php

    $config['base_url'] = 'http://localhost/';

    $config['index_page'] = '';

## 配置 Nginx

```bash
$ sudo vi /etc/nginx/conf.d/default.conf

    server {
            listen 80;
            #listen [::]:80 ipv6only=on;
            server_name  localhost;
            root /home/shenlin/zaimusic/www;
            index index.php  index.html index.htm;

            location / {
                    # 这里使用try_files进行url重写，不用rewrite了。
                    try_files $uri $uri/ /index.php?$query_string;
            }

            location ~ \.php($|/) {
                fastcgi_pass   127.0.0.1:9000;
                fastcgi_index  index.php;
                fastcgi_split_path_info ^(.+\.php)(.*)$;
                fastcgi_param   PATH_INFO $fastcgi_path_info;
                fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
                include        fastcgi_params;
            }

            location ~ /\.ht {
                    deny  all;
            }
    }

$ sudo nginx -s reload
```

可以测试一下效果（当然前提是 nginx, php-fpm, selinux, firewall 都设置好了）
