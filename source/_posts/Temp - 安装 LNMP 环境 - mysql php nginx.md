---
title: Linux - 安装配置 LNMP 环境
categories:
  - Linux
tags:
  - Linux
  - Nginx
  - MySQL
  - PHP
---

安装 Apache/Nginx + MySQL + PHP 的正确顺序应该是：LMPA（MySQL、PHP、Apache）。

原因很简单：后面的软件（有可能）依赖前面的软件，即自底向上安装是一般的规律。

MySQL在安装时会带一个MySQL的函数库，而这个函数库在安装PHP时会用到。

PHP安装成功后会生成一个php-fpm进程提供fastcgi服务，安装好Apache或者Nginx如果要执行PHP需要进行相关设置。

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

## PHP

```bash

```

## Nginx

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
