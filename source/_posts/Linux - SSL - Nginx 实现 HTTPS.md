---
title: Linux - SSL - Nginx 实现 HTTPS
categories:
  - Linux
tags:
  - SSL
  - Nginx
  - HTTPS
---

Nginx 实现 HTTPS

<!--more-->

## 实践

查询是否安装ssh
```bash
rpm -qa | grep ssh
```

重启SSH服务
```
service sshd restart
```

查看是否启动22端口
```
netstat -antp | grep sshd
```

设置ssh服务开机启动
```
chkconfig sshd on 
```

## 

## 修改链接

下一步，网页加载的 HTTP 资源，要全部改成 HTTPS 链接。因为加密网页内如果有非加密的资源，浏览器是不会加载那些资源的。

    <script src="http://foo.com/jquery.js"></script>

上面这行加载命令，有两种改法。

    <!-- 改法一 -->
    <script src="https://foo.com/jquery.js"></script>

    <!-- 改法二 -->
    <script src="//foo.com/jquery.js"></script>

其中，改法二会根据当前网页的协议，加载相同协议的外部资源，更灵活一些。

另外，如果页面头部用到了rel="canonical"，也要改成HTTPS网址。


    <link rel="canonical" href="https://foo.com/bar.html" />


## 301 重定向

下一步，修改 Web 服务器的配置文件，使用 301 重定向，将 HTTP 协议的访问导向 HTTPS 协议。

Nginx 的写法。
```
server {
  listen 80;
  server_name domain.com www.domain.com;
  return 301 https://domain.com$request_uri;
}
```

Apache 的写法（.htaccess文件）。
```
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
```

