# chapter 4.3 : 搭建 git 服务器

tags ： AAA Book-ProGit git

---

## 导出裸仓库

把现有仓库导出为裸仓库，按照惯例，裸仓库目录名以 .git 结尾

导出裸仓库就是只取出 git 仓库自身，不要工作目录，并为裸仓库单独创建一个 .git 结尾目录

```
//不能在项目目录下导
ssl@sslnotebook MINGW32 /d/gitgo (master)
$ git clone --bare gitgo gitgo.git
fatal: repository 'gitgo' does not exist

//进入项目上级目录
ssl@sslnotebook MINGW32 /d/gitgo (master)
$ cd ..

//使用 --bare 选项导出裸仓库
ssl@sslnotebook MINGW32 /d
$ git clone --bare gitgo gitgo.git
Cloning into bare repository 'gitgo.git'...
done.

//导出的裸仓库和项目位于同级目录，内容是 gitgo/.git 的文件
ssl@sslnotebook MINGW32 /d
$ ll gitgo.git
total 16
-rw-r--r-- 1 ssl 197121 138 七月  5 06:50 config
-rw-r--r-- 1 ssl 197121  73 七月  5 06:50 description
-rw-r--r-- 1 ssl 197121  23 七月  5 06:50 HEAD
drwxr-xr-x 1 ssl 197121   0 七月  5 06:50 hooks/
drwxr-xr-x 1 ssl 197121   0 七月  5 06:50 info/
drwxr-xr-x 1 ssl 197121   0 七月  5 06:50 objects/
-rw-r--r-- 1 ssl 197121 542 七月  5 06:50 packed-refs
drwxr-xr-x 1 ssl 197121   0 七月  5 06:50 refs/
```

效果和下面的命令大致相同，但不完全一样，下面的命令是完全复制，包含的文件比 `git clone --bare` 要多
```
$ git cp -Rf gitgo/.git gitgo.git
```

## 把裸仓库放服务器上

把裸仓库放到服务器`example.com`的`/opt/git`目录下
```
$ scp -r gitgo.git user@example.com:/opt/git
```
对目录 `/opt/git` 有读权限的用户可以通过 SSH 克隆仓库
```
$ git clone user@example.com:/opt/git/gitgo.git
```
对 `/opt/git` 目录有写权限的用户将自动拥有推送权限
```
$ git push
```
在该项目目录中运行 git init 命令，并加上 --shared 选项，那么 Git 会自动修改该仓库目录的组权限为可写。
```
$ ssh user@git.example.com
$ cd /opt/git/gitgo.git
$ git init --bare --shared
```

---

架设一个几个人拥有连接权的 git 服务器的简单步骤：
1. 导出裸仓库
2. 把裸仓库放入大家有读写权限的目录
3. 服务器上加入可以使用 SSH 协议访问的账号

更多设定：
如何避免为每一个用户建立一个账户
给仓库添加公共读取权限
架设网页界面
……


架设 Git 服务最复杂的地方在于用户管理。如果需要仓库对特定的用户可读，而给另一部分用户读写权限，那么访问和许可安排就会比较困难。


给团队每个成员提供访问权的方法： 

1. 给团队里的每个人创建账号，这种方法很直接但也很麻烦。要为每个人运行一次 adduser 并且设置临时密码，然后使用服务器操作系统的文件系统权限在仓库上设置更复杂的访问控制权限。

2. 如果需要团队里的每个人都对仓库有写权限，又不能给每个人在服务器上建立账户，那么提供 SSH 连接就是唯一的选择了。 

    2.1 在主机上建立一个 git 账户，让每个需要写权限的人发送一个 SSH 公钥，然后将其加入 git 账户的 ~/.ssh/authorized_keys 文件。这样所有人都将通过 git 账户访问主机。 这一点也不会影响提交的数据——访问主机用的身份不会影响提交对象的提交者信息。
    
    2.2 让 SSH 服务器通过某个 LDAP 服务，或者其他已经设定好的集中授权机制，来进行授权。只要每个用户可以获得主机的 shell 访问权限，任何 SSH 授权机制都可视为是有效的。

---

## 设置协议

### 配置 SSH 协议


#### 设置 SSH 连接


#### 生成 SSH 公钥

##### 公钥文件

* 私钥文件：id_dsa 或 id_rsa
* 公钥文件：id_dsa_pub 或 id_rsa_pub

##### 保存位置

linux 一般位于 `~/.ssh`
windows 一般位于 `c:\users\username\.ssh`

##### 生成公钥

确认当前用户是否已经有 SSH 公钥，查看 SSH 公钥的保存目录：
```
$ ll ~/.ssh
```

没有 SSH 公钥，则使用命令生成
```
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ssy/.ssh/id_rsa): 
/home/ssy/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/ssy/.ssh/id_rsa.
Your public key has been saved in /home/ssy/.ssh/id_rsa.pub.
The key fingerprint is:
ca:dc:d4:50:cf:06:e6:10:2b:cf:62:11:c8:8a:5e:52 ssy@e42.matrix
The key's randomart image is:
+--[ RSA 2048]----+
|   . .. o.+      |
|   Eo  . * +     |
| ...  o o . +    |
|....   = o .     |
|. o   o S .      |
| .   + =         |
|      + .        |
|                 |
|                 |
+-----------------+
```
* 可重复生成，会提示你是否覆盖
* 生成的过程中会要求输入秘钥口令，如果不想使用秘钥的过程中输入口令，直接回车即可

查看 SSH 公钥
```
$ cat .ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAvjO/J+qlOOr7Hd7x4gpJoZ2DU7gtI8V6juE60ULXzMGezQFce56JB2dGOp5iUgAXzLVv8p40KURjiOO8El1UVC6baibsTzDTP9hUu1oHA/a9R6iuz03P43hYYPtuCZu34sMHAlaj+ufzAR6loPeD3ubtKgjKH+nR0A8BsZGSdzyiofhJSz+APYxvtOovWCkjZ09t5qffmcLcZ/S53LguXvLnirgmxMzSruJJ3Y8y6YpEKDdkeIWThV+kojIOxsudVWGVxXYInZe0te/LzwvHITtlJh7tzFnKgSwwQIxuyETqLacbWKg4qsnmuAc22M5aGzI3X2DIMLRGnSSREctl0Q== ssy@bogon
```
生成后可使用邮件等方式把公钥发送给 git 管理员

#### 配置 SSH 访问

使用 authorized_keys 方法对用户进行认证。

创建用户 git，并建立目录 .ssh
```
$ sudo adduser git
$ su git
$ cd ~
$ mkdir .ssh && chmod 700 .ssh
$ touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys
```
把公钥文件导入 authorized_keys 文件
```
$ cat /tmp/id_rsa.john.pub >> ~/.ssh/authorized_keys
$ cat /tmp/id_rsa.mike.pub >> ~/.ssh/authorized_keys
$ cat /tmp/id_rsa.jessica.pub >> ~/.ssh/authorized_keys
```
建立裸仓库
```
$ cd /opt/git
$ mkdir project.git
$ cd project.git
$ git init --bare
Initialized empty Git repository in /opt/git/project.git/
```
john把自己的推送文件到裸库
```
$ cd johnProject
$ git init
$ git add .
$ git commit -m 'initial commit'
$ git remote add origin git@gitserver:/opt/git/project.git
$ git push origin master
```
其他开发者可以克隆仓库并推送自己的改动
```
$ git clone git@gitserver:/opt/git/project.git
$ cd project
$ vim readme
$ git commit -m 'modified the readme file'
$ git push origin master
```

#### 关于 shell 权限

上述例子中所有的开发者都是以 linux 系统用户 git 身份访问 git 仓库，这意味着他们也可以使用这个 git 身份登录服务器并执行 bash 命令，通常我们只想让开发者凭借 git 这个身份使用 SSH 协议连接到服务器推送和拉取 git 仓库……

这时可以使用 `chsh` 命令修改 git 用户的 shell：
```
//两个命令效果相同
$ sudo chsh -s $(which git-shell) git
$ sudo chsh -s /usr/bin/git-shell git
Changing shell for git.
Shell changed.

//也可直接修改passwd文件
$ sudo vi /etc/passwd
```
* git-shell 是一个受限 shell 工具，可以把 git 用户的活动限制在 git 相关的范围之内
* git-shell 由 git 软件包提供
* 更改 shell 前需要把 git-shell 的路径加入 /etc/shells 文件，否则会提示 Warning: "/usr/bin/git-shell" is not listed in /etc/shells.

再次用 git 登录则会提示：
```
$ ssh git@gitserver
fatal: Interactive git shell is not enabled
hint: ~/git-shell-commands should exist and have read and execute access.
Connection to gitserver closed.

$ su git
fatal: What do you think I am? A shell?
```
* 可以在 git 用户的家目录下面建立 git-shell-commands 目录
* 此目录允许你对 git-shell 命令进行一定程度的自定义
    * 可以限制掉某些本应被服务器接受的 Git 命令
    * 对 SSH 拒绝登录信息进行自定义
 

###  配置 git 协议的守护进程

使用 git 协议的仓库是基于守护进程的。

git-daemon 是独立于 git 的另外一个软件包。

#### git-daemon-export-ok 和 whitelist

守护进程 git daemon 默认会寻找 git 库根目录下的一个神奇文件 git-daemon-export-ok，当有这个文件时，表示允许 git 协议无授权的访问，但是如果 git daemon 使用了选项 --export-all，则无论目录是否有此神奇文件，只要目录包含了objects和refs两个子目录看起来像 git 库都会允许无授权的访问

git daemon 命令后面可有多个空格隔开的目录，如`git daemon /pub/bar /pub/foo`这是白名单目录，是明确允许无授权访问的目录

git daemon 如果使用了 --strict-paths 选项，但没有指定白名单，则 git daemon 不会启动

???测试有了白名单，神奇文件是否还起作用

#### 运行守护进程
    
* **注意**：使用交互式命令行直接运行下面命令则会停留在此处，不会出现新的命令提示符，Ctrl+c 结束此命令则 git-daemon 进程结束，也可以先用 Ctrl+z 把命令放入挂起到后台，然后用`jobs`查看其任务号 n，再用`bg n`让命令在后台运行
```
$ git daemon --reuseaddr --base-path=/opt/git /opt/git
```
* --reuseaddr
    * 绑定监听套接字时使用 SO_REUSEADDR 
    * 允许服务器在无需等待旧连接超时的情况下重启
* --base-path
    * 指定 git 仓库的根路径，用户 git 协议中的路径被作为相对路径来看待
    * 如 git://exmaple.com/hello.git，则将被解释为 /opt/git/hello.git 
* 结尾的路径是白名单告诉 Git 守护进程从何处寻找仓库来导出。
* 如果有防火墙正在运行，需要开放端口 9418 的通信权限
* 出于安全考虑，强烈建议使用一个对仓库拥有只读权限的用户身份来运行该守护进程，如新建用户git-ro

#### 创建 git-daemon-export-ok 文件

告诉 Git 哪些仓库允许基于服务器的无授权访问
```
$ cd /path/to/project.git
$ touch git-daemon-export-ok
```
* 有 git-daemon-export-ok 文件的仓库表示允许 Git 提供无需授权的项目访问服务

### 配置 Smart HTTP 协议

git 自带的 CGI 脚本 git-http-backend 会读取由 git fetch 或 git push 命令发送的 HTTP 请求路径和头部信息，来判断该客户端是否支持 HTTP 智能（Smart）模式，若支持 git 将会以智能模式与客户端进行通信，否则会回落到哑（Dumb）模式下（对某些老的客户端实现向下兼容）。

#### 安装 CGI 服务器和相关模块

安装 Apache 作为 CGI 服务器，并启用所需的模块：mod_cgi, mod_alias, mod_env
```
$ sudo yum install -y apache2 apache2-utils
$ a2enmod cgi alias env
```

#### 配置 CGI 服务器

1. 配置 apache 文件让 git-http-backend 来处理 /git 路径的请求
```
SetEnv GIT_PROJECT_ROOT /opt/git
SetEnv GIT_HTTP_EXPORT_ALL
ScriptAlias /git/ /usr/lib/git-core/git-http-backend/
```
* 留空 GIT_HTTP_EXPORT_ALL 这个环境变量，Git 将只对无授权客户端提供带 git-daemon-export-ok 文件的版本库，就像 Git 守护进程一样

**下面的2，3步中英文不一样啊，应该是殊途同归，英文版的库路径是/srv/git**

2. 英文版
You’ll also need to set the Unix user group of the /srv/git directories to www-data so your web server can read- and write-access the repositories, because the Apache instance running the CGI script will (by default) be running as that user:

apache 运行 CGI 脚本默认是以用户组 www-data 身份运行，所以要设置 git 库目录的所属用户组为 www-data 用户组，才有读写权限
```
$ chgrp -R www-data /srv/git
```
* 我的没有这个用户组???

2. 中文版
让 Apache 接受通过该路径的请求
```
<Directory "/usr/lib/git-core*">
   Options ExecCGI Indexes
   Order allow,deny
   Allow from all
   Require all granted
</Directory>
```

3. 中文版
实现写操作授权验证
```
<LocationMatch "^/git/.*/git-receive-pack$">
    AuthType Basic
    AuthName "Git Access"
    AuthUserFile /opt/git/.htpasswd
    Require valid-user
</LocationMatch>
```
* 需要创建一个包含所有合法用户密码的 .htpasswd 文件。
* 下面是添加 “schacon” 用户到此文件的例子：
```
//中文版
$ htdigest -c /opt/git/.htpasswd "Git Access" schacon

//英文版
$ htpasswd -c /srv/git/.htpasswd schacon
```

3. 英文版
tell Apache to allow requests to git-http-backend and make writes be authenticated somehow, possibly with an Auth block like this:

让 Apache 允许访问 git-http-backend，并且实现写操作授权
```
<Files "git-http-backend">
    AuthType Basic
    AuthName "Git Access"
    AuthUserFile /srv/git/.htpasswd
    Require expr !(%{QUERY_STRING} -strmatch '*service=git-receive-pack*' || %{REQUEST_URI} =~ m#/git-receive-pack$#)
    Require valid-user
</Files>
```