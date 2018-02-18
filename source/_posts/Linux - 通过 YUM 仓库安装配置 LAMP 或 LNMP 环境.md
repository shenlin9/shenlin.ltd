---
title: Linux - 通过 YUM 仓库安装配置 LAMP 或 LNMP 环境
categories:
  - Linux
tags:
  - Linux
  - YUM
  - Nginx
  - MySQL
  - PHP
---

安装 Apache/Nginx + MySQL + PHP 的正确顺序应该是：LMPA（MySQL、PHP、Apache）。

原因很简单：后面的软件（有可能）依赖前面的软件，即自底向上安装是一般的规律。

MySQL在安装时会带一个MySQL的函数库，而这个函数库在安装PHP时会用到。

PHP安装成功后会生成一个php-fpm进程提供fastcgi服务，安装好Apache或者Nginx如果要执行PHP需要进行相关设置。

???以上存疑

## php 和 apache,nginx 的安装顺序

php 在 configure 时，

如果以模块化方式和web服务器链接，则需要设定选项：--with-apxs2=/usr/local/apache2/bin/apxs ，

很明显需要提前安装 apache

如果以 FastCGI 或 PHP-FPM 连接服务器，则 ...

如果 php 的 --with-mysql=/usr/local/mysql/bin/mysql_config 则很明显应该安装 mysql，

当然现在一般都是使用 mysqlnd，所以顺序呢 ???

<!--more-->

## 安装配置 MySQL

可以通过 YUM 源来安装 MySQL ，首先需要把 MySQL 的 yum 源配置到系统 yum 源

列表，可以通过安装一个 MySQL 提供的 RPM 包增加 MySQL 的 yum 仓库到系统仓库

列表。

然后就可以直接 `yum install mysql` 了

需要注意的是，一旦完成了 MySQL 的 YUM 源在系统的配置，则通过 `yum update`

触发的任何系统级的更新也会在 YUM 源上查找 MySQL 的更新，并且替换第三方包。

```bash
# 列出系统yum仓库
$ yum repolist

# 列出启用的yum仓库
$ yum repolist --enabled
```

### 下载 YUM 源的 RPM 安装包

从官网的Yum Repository下载rpm包

--> 进入 www.mysql.com
--> 导航链接 downloads
--> 导航链接 community
--> 页面链接 MySQL Community Server (GPL)
--> 选项卡 Generally Available(GA) Release
--> 选择 OS 和 OS Version
--> 图片链接 Install Using Yum
--> 进入下载页面选择对应的 RPM 包，如 Red Hat Enterprise Linux 7 / Oracle Linux 7 (Architecture Independent), RPM Package 

### 通过 RPM 安装 MySQL 的 YUM 源


```bash
# 安装之前查看目前启用的 yum 源
[shenlin@t460p /usr/local/src]$ yum repolist enabled
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
repo id                  repo name                                       status
!base/7/x86_64           CentOS-7 - Base - 163.com                       9,591
!extras/7/x86_64         CentOS-7 - Extras - 163.com                       329
!updates/7/x86_64        CentOS-7 - Updates - 163.com                    1,720
repolist: 11,640

# 下载 mysql 的 yum 源
[shenlin@t460p /usr/local/src]$ sudo wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm

# 查看此安装包要安装的文件
[shenlin@t460p /usr/local/src]$ rpm -qpl mysql57-community-release-el7-11.noarch.rpm
warning: mysql57-community-release-el7-11.noarch.rpm: Header V3 DSA/SHA1 Signature, key ID 5072e1f5: NOKEY
/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
/etc/yum.repos.d/mysql-community-source.repo
/etc/yum.repos.d/mysql-community.repo

# 开始安装，安装程序会自动帮我们配置好 MySQL 的 yum 源
# 这里推荐使用 yum install 命令，
# 因为如果给出了一个文件名的话，install命令也会执行本地安装的操作
# 而 localinstall 仅仅因为历史遗留的原因而被维护着
[shenlin@t460p /usr/local/src]$ sudo yum localinstall mysql57-community-release-el7-11.noarch.rpm

# 安装之后对比可以发现多了 MySQL 的 yum 源
[shenlin@t460p /usr/local/src]$ yum repolist enabled
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
repo id                             repo name                           status
base/7/x86_64                       CentOS-7 - Base - 163.com           9,591
extras/7/x86_64                     CentOS-7 - Extras - 163.com           329
updates/7/x86_64                    CentOS-7 - Updates - 163.com        1,909
mysql-connectors-community/x86_64   MySQL Connectors Community             42
mysql-tools-community/x86_64        MySQL Tools Community                  55
mysql57-community/x86_64            MySQL 5.7 Community Server            247
repolist: 12,173

# 因为我们下载的是 mysql57 的 rpm 包，所以默认启用的是mysql57，但如果要安装其他版本mysql
# 方法一：
[shenlin@t460p /usr/local/src]$ sudo yum-config-manager --disable mysql57-community # 禁用 mysql57
[shenlin@t460p /usr/local/src]$ sudo yum-config-manager --enable mysql56-community  # 启用 mysql56

# 方法二：直接编辑 mysql 的 yum 源文件，把要启用的版本 enabled 改为 1
# 注意当多个版本 enabled=1 时，默认启动最新版本的MySQL安装
[shenlin@t460p /usr/local/src]$ cat /etc/yum.repos.d/mysql-community.repo
[mysql56-community]
name=MySQL 5.6 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/7/$basearch/
enabled=0       # 改为1即启用
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

### 通过 YUM 源安装 MySQL 

```bash
[shenlin@t460p /usr/local/src]$ sudo yum install -y mysql-community-server 
```

### MySQL 的启动停止

下面的有些而命令分别使用 sysvinit 和 systemd 实现
```bash
# 更改 MySQL 字符集
[shenlin@t460p ~]$ sudo vi /etc/my.cnf
[mysqld]
character_set_server = utf8

# 启动服务
service mysqld start
systemctl start mysqld.service

# mysql 服务第一次运行时，
# 在 MySQL 里面创建了一个超级用户’root’@’localhost’
# 系统自动设置了超级账户的临时密码，被存储到了 log 文件中了
# 查看密码
[shenlin@t460p /usr/local/src]$ sudo grep 'temporary password' /var/log/mysqld.log
[sudo] password for shenlin:
2018-01-27T05:06:25.106683Z 1 [Note] A temporary password is generated for root@localhost: bH6ooes)xTbo

# 使用临时密码登录 MySQL，然后为超级账户设置一个自定义的密码
[shenlin@t460p /usr/local/src]$ mysql -uroot -pbH6ooes)xTbo

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'UU4xiaopihai!';
Query OK, 0 rows affected (0.00 sec)

# 还有种方法，直接使用下面的命令，无需密码进入 mysql
# 会进行向导式设置，其中就包括更改 root 密码
[shenlin@t460p ~]$ mysql_secure_installation

Securing the MySQL server deployment.

#       是否更改 root 密码？
#       安装了插件，对密码有要求:必须包含大小写字母，数字，特殊符号，长度 8 位以上
Enter password for user root:
The 'validate_password' plugin is installed on the server.                  
The subsequent steps will run with the existing configuration of the plugin.
Using existing password for root.

Estimated strength of the password: 100
Change the password for root ? ((Press y|Y for Yes, any other key for No) : n

 ... skipping.

#       是否删除匿名用户？
#       MySQL 安装时添加了一个匿名用户，允许任何人没有 mysql 账号就可以直接访问 mysql
#       其目的是为了测试和使得安装过程更顺利，但生产环境一定要移除这个匿名用户
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : n

 ... skipping.

#       是否不允许 root 用户远程连接？
#       一般情况下应该只允许 root 用户从本地连接到 mysql
#       这样可以确保任何人都无法通过网络猜中密码
Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : n

 ... skipping.

#       是否删除测试数据库 test？
#       MySQL 安装程序添加了一个测试数据库 test，
#       任何人都可以访问，目的是为了测试，
#       在生产环境应该删除
By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.

Remove test database and access to it? (Press y|Y for Yes, any other key for No) : n

 ... skipping.

#       是否马上重新载入权限表，以确保以上的修改可以立即生效？
Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : n

 ... skipping.

All done!

# 查看服务状态
[shenlin@t460p /usr/local/src]$ service mysqld status
[shenlin@t460p /usr/local/src]$ systemctl status mysqld.service

# 设置服务随系统自动启动
???
systemctl enable mysqld.service

# 停止服务
service mysqld stop
systemctl stop mysqld.service
```

## Apache

### yum 安装
```
[shenlin@t460p ~]$ sudo yum install -y httpd
Installing : httpd-tools-2.4.6-67.el7.centos.6.x86_64
Installing : mailcap-2.1.41-2.el7.noarch
Installing : httpd-2.4.6-67.el7.centos.6.x86_64
```

## Nginx

### yum 安装

https://www.nginx.com/resources/wiki/start/topics/tutorials/install/

配置 nginx 仓库
```
[shenlin@t460p ~]$ cd /etc/yum.repos.d
[shenlin@t460p /etc/yum.repos.d]$ sudo touch nginx.repo
[shenlin@t460p /etc/yum.repos.d]$ sudo vi nginx.repo
    [nginx]
    name=nginx repo
    baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
    gpgcheck=0
    enabled=1
```

安装 nginx
```
[shenlin@t460p /etc/yum.repos.d]$ sudo yum install -y nginx
```

### 源码安装

```bash
# 根据 LSB 规定的源码存放目录
[shenlin@t460p ~]$ cd /usr/local/src

# 下载 Nginx 的 stable 版本
# 没有 sudo 则提示 Permission denied，保存文件失败
[shenlin@t460p /usr/local/src]$ sudo wget http://nginx.org/download/nginx-1.12.2.tar.gz

# 下载 PGP 签名
[shenlin@t460p /usr/local/src]$ sudo wget http://nginx.org/download/nginx-1.12.2.tar.gz.asc

# 验证 PGP 签名
[shenlin@t460p /usr/local/src]$ gpg --verify nginx-1.12.2.tar.gz.asc
```

## PHP

### yum 安装

```
sudo yum install -y \
php70w php70w-cli php70w-common php70w-mysqlnd php70w-pdo php70w-pdo_dblib \
php70w-mbstring php70w-mcrypt php70w-gd php70w-opcache php70w-bcmath \
php70w-fpm
```

没有 mod_php70w，这个是apache模块，只有 mod_php71w

php70w-mysql        和 php70w-mysqlnd 冲突
php70w-imap
php70w-interbase
php70w-intl
php70w-ldap
php70w-pdo_dblib
php70w-devel
php70w-pear.noarch
php70w-pecl-apcu
php70w-pecl-apcu-devel
php70w-pecl-geoip
php70w-pecl-igbinary
php70w-pecl-igbinary-devel
php70w-pecl-imagick
php70w-pecl-imagick-devel
php70w-pecl-libsodium
php70w-pecl-memcached
php70w-pecl-mongodb
php70w-pecl-xdebug
php70w-pgsql
php70w-phpdbg
php70w-process
php70w-pspell
php70w-recode
php70w-snmp
php70w-soap
php70w-tidy
php70w-xml
php70w-xmlrpc
php70w-dba
php70w-embedded
php70w-enchant
php70w-odbc

启用 PHP-FPM 和 Nginx 服务
```
[shenlin@t460p /etc/yum.repos.d]$ systemctl enable nginx --now
[shenlin@t460p /etc/yum.repos.d]$ systemctl enable php-fpm --now
```

查看防火墙设置
```
[shenlin@t460p /etc/yum.repos.d]$ sudo firewall-cmd --list-all --zone=public
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8
  sources:
  services: ssh dhcpv6-client
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

设置防火墙允许http访问
```
[shenlin@t460p ~]$ sudo firewall-cmd --permanent --zone=public --add-service=http
[shenlin@t460p ~]$ sudo firewall-cmd --reload

[shenlin@t460p ~]$ sudo firewall-cmd --list-all --zone=public
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8
  sources:
  services: ssh dhcpv6-client http  # 增加了 http 访问
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

设置 nginx 把 php 请求转发到 php-fpm
```
# 编辑 nginx 配置文件
[shenlin@t460p ~]$ sudo vi /etc/nginx/conf.d/default.conf

    server {
        listen       80;
        server_name  localhost;
        root   /usr/share/nginx/html;

        location / {
            index  index.php index.html index.htm;
        }

        location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;          # php-fpm 在 9000 端口监听
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }
    }

# 确保上面配置文件涉及的参数在下面的 nginx 参数文件里都有对应取值
[shenlin@t460p ~]$ sudo vi /etc/nginx/fastcgi_params
fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
fastcgi_param  SCRIPT_NAME        $fastcgi_script_name;
fastcgi_param  DOCUMENT_ROOT      $document_root;

# nginx 重新载入配置文件
[shenlin@t460p /etc/nginx/conf.d]$ sudo nginx -s reload

# 确认 php-fpm 在 9000 端口监听
[shenlin@t460p /usr/share/nginx/html]$ netstat -tln | grep 9000
tcp        0      0 127.0.0.1:9000          0.0.0.0:*               LISTEN
```

访问 localhost 结果 html 文件提示 403 forbidden，php 文件提示 file not found
```bash
# 查看日志文件
[shenlin@t460p /usr/share/nginx/html]$ sudo vi /var/log/nginx/error.log

        # html 文件日志
2018/01/29 16:03:53 [error] 5143#5143: *29 open() "/home/shenlin/zaimusic/err.html" failed (13: Permission denied), client: 10.0.3.2, server: localhost, request: "GET /err.html HTTP/1.1", host: "localhost"

        # php 文件日志
2018/01/29 16:12:17 [error] 5143#5143: *35 FastCGI sent in stderr: "Primary script unknown" while reading response header from upstream, client: 10.0.3.2, server: localhost, request: "GET /index.php HTTP/1.1", upstream: "fastcgi://127.0.0.1:9000", host: "localhost"

# 修改目录权限
[shenlin@t460p /usr/share/nginx/html]$ chomod -R 777 zaimusic

# nginx 用户，php-fpm 用户，站点目录用户不一致
[shenlin@t460p ~]$ ps aux | grep "nginx"
root    1002  0.0  0.1  46308   976 ?      Ss   16:39   0:00 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
nginx   1003  0.0  0.4  46724  2404 ?      S    16:39   0:00 nginx: worker process

[shenlin@t460p ~]$ ps aux | grep "php-fpm"
root      1250  0.1  5.9 461556 29016 ?     Ss   16:48   0:00 php-fpm: master process (/etc/php-fpm.conf)
apache   1251  0.0  1.0 461556  4936 ?     S    16:48   0:00 php-fpm: pool www
apache   1252  0.0  1.0 461556  4936 ?     S    16:48   0:00 php-fpm: pool www
apache   1253  0.0  1.0 461556  4936 ?     S    16:48   0:00 php-fpm: pool www
apache   1254  0.0  1.0 461556  4936 ?     S    16:48   0:00 php-fpm: pool www
apache   1255  0.0  1.5 461896  7564 ?     S    16:48   0:00 php-fpm: pool www

[shenlin@t460p ~]$ ll
drwxrwx---. 2 shenlin shenlin 39 Jan 29 14:24 zaimusic

# 修改 nginx 的启动用户为目录所属用户
[shenlin@t460p /usr/share/nginx/html]$ sudo vi /etc/nginx/nginx.conf
user  shenlin;

# 重新载入 Nginx 配置文件
[shenlin@t460p ~]$ sudo nginx -s reload

# 修改 php-fpm 的启动用户为目录所属用户
[shenlin@t460p ~]$ sudo vi /etc/php-fpm.d/www.conf

    [www]
    user = shenlin
    group = shenlin

# 重启 PHP-FPM
[shenlin@t460p ~]$ sudo systemctl restart php-fpm

# 停止 selinux
[shenlin@t460p /usr/share/nginx/html]$ sudo vi /etc/selinux/config

    SELINUX=disable
    SELINUXTYPE=targeted

# 不停止 selinux 方法
# 先确认 deined
[shenlin@t460p ~]$ sudo cat /var/log/audit/audit.log | grep php-fpm | grep denied

    type=AVC msg=audit(1517292196.168:252): avc:  denied  { open } for  pid=1003 comm="php-fpm" path="/home/shenlin/zaimusic/index.php" dev="dm-0" ino=4440113 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:user_home_t:s0 tclass=file

[shenlin@t460p ~]$ sudo cat /var/log/audit/audit.log | grep nginx | grep denied

    type=AVC msg=audit(1517214046.233:4876): avc:  denied  { read } for  pid=5308 comm="nginx" name="err.html" dev="dm-0" ino=4440115 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:samba_share_t:s0 tclass=file

# 清空日志
[shenlin@t460p /home/shenlin]$ sudo echo > /var/log/audit/audit.log

[shenlin@t460p /home/shenlin]$ sudo cat /var/log/audit/audit.log

# 访问一次页面后再看日志
[shenlin@t460p /home/shenlin]$ sudo cat /var/log/audit/audit.log

    type=AVC msg=audit(1517293027.968:288): avc:  denied  { open } for  pid=1005 comm="php-fpm" path="/home/shenlin/zaimusic/index.php" dev="dm-0" ino=4440113 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:user_home_t:s0 tclass=file
    type=SYSCALL msg=audit(1517293027.968:288): arch=c000003e syscall=2 success=no exit=-13 a0=7fff5e004920 a1=0 a2=1b6 a3=3 items=0 ppid=952 pid=1005 auid=4294967295 uid=1000 gid=1000 euid=1000 suid=1000 fsuid=1000 egid=1000 sgid=1000 fsgid=1000 tty=(none) ses=4294967295 comm="php-fpm" exe="/usr/sbin/php-fpm" subj=system_u:system_r:httpd_t:s0 key=(null)
    type=PROCTITLE msg=audit(1517293027.968:288): proctitle=7068702D66706D3A20706F6F6C20777777

# 安装日志分析工具
[shenlin@t460p /home/shenlin]$ sudo yum install -y setroubleshoot

# 输出分析日志到文件并查看
[shenlin@t460p /home/shenlin]$ sudo sealert -a /var/log/audit/audit.log > log1

[shenlin@t460p /home/shenlin]$ sudo cat log1

    found 1 alerts in /var/log/audit/audit.log
    --------------------------------------------------------------------------------

    SELinux is preventing /usr/sbin/php-fpm from open access on the file /home/shenlin/zaimusic/index.php.

    *****  Plugin catchall_boolean (89.3 confidence) suggests   ******************

    If you want to allow httpd to read user content
    Then you must tell SELinux about this by enabling the 'httpd_read_user_content' boolean.

    Do
    setsebool -P httpd_read_user_content 1

    *****  Plugin catchall (11.6 confidence) suggests   **************************

    If you believe that php-fpm should be allowed open access on the index.php file by default.
    Then you should report this as a bug.
    You can generate a local policy module to allow this access.
    Do
    allow this access for now by executing:
    # ausearch -c 'php-fpm' --raw | audit2allow -M my-phpfpm
    # semodule -i my-phpfpm.pp


    Additional Information:
    Source Context                system_u:system_r:httpd_t:s0
    Target Context                unconfined_u:object_r:user_home_t:s0
    Target Objects                /home/shenlin/zaimusic/index.php [ file ]
    Source                        php-fpm
    Source Path                   /usr/sbin/php-fpm
    Port                          <Unknown>
    Host                          <Unknown>
    Source RPM Packages           php70w-fpm-7.0.27-1.w7.x86_64
    Target RPM Packages
    Policy RPM                    selinux-policy-3.13.1-166.el7_4.7.noarch
    Selinux Enabled               True
    Policy Type                   targeted
    Enforcing Mode                Enforcing
    Host Name                     t460p.shenlin.me
    Platform                      Linux t460p.shenlin.me 3.10.0-693.17.1.el7.x86_64
                                  #1 SMP Thu Jan 25 20:13:58 UTC 2018 x86_64 x86_64
    Alert Count                   1
    First Seen                    2018-01-30 14:17:07 CST
    Last Seen                     2018-01-30 14:17:07 CST
    Local ID                      034d7c78-03dd-4ff8-bd50-10cac1b82a2d

    Raw Audit Messages
    type=AVC msg=audit(1517293027.968:288): avc:  denied  { open } for  pid=1005 comm="php-fpm" path="/home/shenlin/zaimusic/index.php" dev="dm-0" ino=4440113 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:user_home_t:s0 tclass=file


    type=SYSCALL msg=audit(1517293027.968:288): arch=x86_64 syscall=open success=no exit=EACCES a0=7fff5e004920 a1=0 a2=1b6 a3=3 items=0 ppid=952 pid=1005 auid=4294967295 uid=1000 gid=1000 euid=1000 suid=1000 fsuid=1000 egid=1000 sgid=1000 fsgid=1000 tty=(none) ses=4294967295 comm=php-fpm exe=/usr/sbin/php-fpm subj=system_u:system_r:httpd_t:s0 key=(null)

    Hash: php-fpm,httpd_t,user_home_t,file,open

# 根据提示设置 selinux 允许 httpd 读取用户内容
[shenlin@t460p /home/shenlin]$ sudo setsebool -P httpd_read_user_content 1    

# 将 php-fpm 加入 selinux 白名单
[shenlin@t460p /home/shenlin]$ ausearch -c 'php-fpm' --raw | audit2allow -M my-phpfpm
# 或者
[shenlin@t460p /home/shenlin]$ cat /var/log/audit/audit.log | grep nginx | grep denied | audit2allow -M mynginx

# 
[shenlin@t460p /home/shenlin]$ sudo semodule -i my-phpfpm.pp
```

添加web用户
```
$ sudo groupadd www
$ sudo useradd -g www -s /sbin/nologin www
```
