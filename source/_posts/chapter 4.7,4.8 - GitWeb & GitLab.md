title: chapter 4.7,4.8 - GitWeb & GitLab
categories:
  - Git
  - Book-ProGit
tags:
  - Git

---

GitWeb 是一个基于网页的 Git 库简易查看器，使用 CGI 脚本实现。

GitLab 是一个功能更全面的 Git 服务器，是一个数据库支持的 Web 应用。

<!--more-->

## GitWeb

### 临时运行 GitWeb

GitWeb 基于网页，需要先安装 Web 服务器。

打开浏览器里通过 GitWeb 查看当前库 
```
$ git instaweb
$ git instaweb --httpd=lighttpd
$ git instaweb --httpd=webrick
```
* 启动了一个 HTTP 服务器，监听 1234 端口
* --httpd 选项设定 web 服务器
    * 不使用选项则 linux 默认使用 lighttpd，Mac 默认使用 webrick
    * 可以使用任何支持 CGI 或 Perl 的 Web 服务器

关闭服务器
```
$ git instaweb --httpd=webrick --stop
```

### 长期运行 GitWeb

上面的只是临时的启动 HTTP 服务器，如果需要长期运行，则需要设置 Web 服务器运行 GitWeb 的 CGI 脚本。

可以先尝试利用系统的软件包管理命令安装一下 GitWeb，看当前的 linux 发行版是否有独立的 GitWeb 包。
```
$ yum install -y gitweb
```

没有的话则需要从 Git 的源码中手动安装 GitWeb，还可以生成自定义 CGI 脚本
```
$ git clone git://git.kernel.org/pub/scm/git/git.git

$ cd git/

$ make GITWEB_PROJECTROOT="/opt/git" prefix=/usr gitweb
    SUBDIR gitweb
    SUBDIR ../
make[2]: `GIT-VERSION-FILE' is up to date.
    GEN gitweb.cgi
    GEN static/gitweb.js
    
$ sudo cp -Rf gitweb /var/www/
```
* GITWEB_PROJECTROOT 指示 Git 版本库的位置

添加一个虚拟主机，在 Apache 中使用这个 CGI 脚本：
```
<VirtualHost *:80>
    ServerName gitserver
    DocumentRoot /var/www/gitweb
    <Directory /var/www/gitweb>
        Options ExecCGI +FollowSymLinks +SymLinksIfOwnerMatch
        AllowOverride All
        order allow,deny
        Allow from all
        AddHandler cgi-script cgi
        DirectoryIndex gitweb.cgi
    </Directory>
</VirtualHost>
```

访问 http://gitserver/ 在线查看你的版本库


### 实践问题

http://www.cnblogs.com/dudu/archive/2012/12/10/linux-apache-gitweb.html

安装失败貌似和apache的安装路径有关
我的是自定义路径httpd-2.4.25，而不是默认的apache2
/usr/local/httpd-2.4.25/modules/

git instaweb 位置：
/usr/libexec/git-core/git-instaweb
第 60 行
好像是因为路径都写死在里面了，固定的去/usr/local/sbin 和/usr/sbin 

https://stackoverflow.com/questions/1508752/git-instaweb-httpd-configuration-to-use-apache2-on-osx-leopard-server

```
>> git instaweb
fatal: Not a git repository (or any of the parent directories): .git

>> cd /opt/git/project.git/

>> git instaweb
lighttpd not found. Install lighttpd or use --httpd to specify another httpd daemon.

//只有httpd为apache时需要指定路径参数
>> git instaweb --httpd=apache2 --module-path=/usr/local/httpd-2.4.25/modules/
```
https://git-scm.com/docs/git-instaweb

## GitLab

官方网址：https://gitlab.com/gitlab-org/gitlab-ce/tree/master

### 安装

安装 Bitnami GitLab 虚拟机

从 https://bitnami.com/stack/gitlab 下载一键安装包

按 `alt + →` 进入 Bitnami 登录界面， Bitnami 的一个优点在于它的登录界面；会显示安装好的 GitLab 的 IP 地址以及默认的用户名和密码

### 管理



### 使用













