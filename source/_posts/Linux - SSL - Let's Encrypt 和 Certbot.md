---
title: Linux - SSL - Let's Encrypt 和 cerbot
categories:
  - Linux
tags:
  - SSL
  - Let's Encrypt
  - cerbot
---

为了推广 HTTPS 协议，电子前线基金会 EFF（Electronic Frontier Foundation）成立了
Let's Encrypt，提供免费证书。

使用 Let's Encrypt 证书，可以使用自动安装工具 Certbot。

<!--more-->

## Let's Encrypt

ACME (Automatic Certificate Management Environment )

https://letsencrypt.org/


## CertBot

https://certbot.eff.org/
https://certbot.eff.org/docs/

CertBot 是 Let's Encrypt 的客户端，
可以为站点部署 "Let's Encrypt" 证书并自动配置启用 HTTPS

为使用 yum 安装 certbot 启用 EPEL 库
```
$ sudo yum install -y epel-release
```

安装 certbot
```
$ sudo yum install -y certbot
```

安装 certbot-nginx 插件
```
$ sudo yum install -y python2-certbot-nginx
```

安装证书并自动配置 nginx
```
// 使用 localhost 域名出错
$ sudo certbot --nginx
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator nginx, Installer nginx
Enter email address (used for urgent renewal and security notices) (Enter 'c' to
cancel): bitbite@foxmail.com
Starting new HTTPS connection (1): acme-v01.api.letsencrypt.org

-------------------------------------------------------------------------------
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
agree in order to register with the ACME server at
https://acme-v01.api.letsencrypt.org/directory
-------------------------------------------------------------------------------
(A)gree/(C)ancel: A

-------------------------------------------------------------------------------
Would you be willing to share your email address with the Electronic Frontier
Foundation, a founding partner of the Let's Encrypt project and the non-profit
organization that develops Certbot? We'd like to send you email about EFF and
our work to encrypt the web, protect its users and defend digital rights.
-------------------------------------------------------------------------------
(Y)es/(N)o: N
No names were found in your configuration files. Please enter in your domain
name(s) (comma and/or space separated)  (Enter 'c' to cancel): localhost                            // localhost 出错
Obtaining a new certificate
An unexpected error occurred:
The request message was malformed :: Error creating new authz :: DNS name does not have enough labels
Please see the logfiles in /var/log/letsencrypt for more details.

IMPORTANT NOTES:
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.

// 使用 www.shenlin.ltd 域名出错
$ sudo certbot --nginx
[sudo] password for shenlin:
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator nginx, Installer nginx
Starting new HTTPS connection (1): acme-v01.api.letsencrypt.org
No names were found in your configuration files. Please enter in your domain
name(s) (comma and/or space separated)  (Enter 'c' to cancel): www.shenlin.ltd
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for www.shenlin.ltd
Using default address 80 for authentication.
Waiting for verification...
Cleaning up challenges
Failed authorization procedure. www.shenlin.ltd (http-01): urn:acme:error:connection :: The server could not connect to the client to verify the domain :: DNS problem: NXDOMAIN looking up A for www.shenlin.ltd

IMPORTANT NOTES:
 - The following errors were reported by the server:

   Domain: www.shenlin.ltd
   Type:   connection
   Detail: DNS problem: NXDOMAIN looking up A for www.shenlin.ltd

   To fix these errors, please make sure that your domain name was
   entered correctly and the DNS A/AAAA record(s) for that domain
   contain(s) the right IP address. Additionally, please check that
   your computer has a publicly routable IP address and that no
   firewalls are preventing the server from communicating with the
   client. If you're using the webroot plugin, you should also verify
   that you are serving files from the webroot path you provided.

$ sudo certbot --apache -d www.example.com
```

安装证书手动配置 nginx
```
$ sudo certbot --nginx certonly
```

Let's Encrypt 证书有 90 天的有效期
certbot 有自动更新证书的命令
```
$ sudo certbot renew
```

可以设置计划任务 cron 或 systemd timer (在过期前)定期自动更新证书
??? 过期前不是说快过期的时候再运行，而是安装上证书后就要开始运行了
最好一天运行两次，防止撤销 Let's Encrypt-initiated 撤销
> if you're setting up a cron or systemd job, we recommend running it twice per day 
> (it won't do anything until your certificates are due for renewal or revoked, but
> running it regularly would give your site a chance of staying online in case a
> Let's Encrypt-initiated revocation happened for some reason).
> 
> Please select a random minute within the hour for your renewal tasks.
```
0 0,12 * * * python -c 'import random; import time; time.sleep(random.random() * 3600)'
&& sudo systemctl stop nginx
&& sudo certbot renew
&& sudo systemctl start nginx
```
??? 要先关闭占用 80 端口的服务，否则提示 'Problem binding to port 80: Could not bind to IPv4 or IPv6.. Skipping.'

当然为保证更新命令运行正常，先测试下
```
$ sudo certbot renew --dry-run
```

## 手工配置

安装成功后，使用 `SSL Labs Server Test` 检查一下证书是否生效
```
$ sudo  
```

## 安装证书

证书可以放在/etc/ssl目录（Linux 系统），然后根据你使用的Web服务器进行配置。

        证书配置文件生成器，by Mozilla
        配置文件模板，by SSLMate

如果使用 Let's Encrypt 证书，请使用自动安装工具 Certbot。

安装成功后，使用 SSL Labs Server Test 检查一下证书是否生效。

## certbot

```
➜  ~ sudo certbot certificates
Saving debug log to /var/log/letsencrypt/letsencrypt.log

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Found the following certs:
  Certificate Name: shenlin.ga
    Domains: shenlin.ga
    Expiry Date: 2019-10-07 00:30:58+00:00 (VALID: 87 days)
    Certificate Path: /etc/letsencrypt/live/shenlin.ga/fullchain.pem
    Private Key Path: /etc/letsencrypt/live/shenlin.ga/privkey.pem
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

➜  ~ sudo certbot renew --dry-run
Saving debug log to /var/log/letsencrypt/letsencrypt.log

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Processing /etc/letsencrypt/renewal/shenlin.ga.conf
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Cert not due for renewal, but simulating renewal for dry run
Plugins selected: Authenticator standalone, Installer None
Starting new HTTPS connection (1): acme-staging-v02.api.letsencrypt.org
Renewing an existing certificate
Performing the following challenges:
http-01 challenge for shenlin.ga
Cleaning up challenges
Attempting to renew cert (shenlin.ga) from /etc/letsencrypt/renewal/shenlin.ga.conf produced an unexpected error: Problem binding to port 80: Could not bind to IPv4 or IPv6.. Skipping.
All renewal attempts failed. The following certs could not be renewed:
  /etc/letsencrypt/live/shenlin.ga/fullchain.pem (failure)

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
** DRY RUN: simulating 'certbot renew' close to cert expiry
**          (The test certificates below have not been saved.)

All renewal attempts failed. The following certs could not be renewed:
  /etc/letsencrypt/live/shenlin.ga/fullchain.pem (failure)
** DRY RUN: simulating 'certbot renew' close to cert expiry
**          (The test certificates above have not been saved.)
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1 renew failure(s), 0 parse failure(s)

IMPORTANT NOTES:
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
```

Certbot 命令：
```
obtain, install, and renew certificates:
    (default) run   Obtain & install a certificate in your current webserver
    certonly        Obtain or renew a certificate, but do not install it
    renew           Renew all previously obtained certificates that are near
                    expiry
    enhance         Add security enhancements to your existing configuration
   -d DOMAINS       Comma-separated list of domains to obtain a certificate for

  (the certbot apache plugin is not installed)
  --standalone      Run a standalone webserver for authentication
  (the certbot nginx plugin is not installed)
  --webroot         Place files in a server's webroot folder for authentication
  --manual          Obtain certificates interactively, or using shell script
                    hooks

   -n               Run non-interactively
  --test-cert       Obtain a test certificate from a staging server
  --dry-run         Test "renew" or "certonly" without saving any certificates
                    to disk

manage certificates:
    certificates    Display information about certificates you have from Certbot
    revoke          Revoke a certificate (supply --cert-path or --cert-name)
    delete          Delete a certificate

manage your account with Let's Encrypt:
    register        Create a Let's Encrypt ACME account
    update_account  Update a Let's Encrypt ACME account
  --agree-tos       Agree to the ACME server's Subscriber Agreement
   -m EMAIL         Email address for important account notifications

More detailed help:

  -h, --help [TOPIC]    print this message, or detailed help on a topic;
                        the available TOPICS are:

   all, automation, commands, paths, security, testing, or any of the
   subcommands or plugins (certonly, renew, install, register, nginx,
   apache, standalone, webroot, etc.)
```

特定话题的帮助，例如：
```
$ certbot --help webroot
```

证书 90 天过期，过期之前使用 renew 子命令更新：
```bash
# 查看状态
$ sudo certbot certificates

# 更新
$ sudo systemctl stop nginx
$ sudo certbot renew
$ sudo systemctl start nginx
```
