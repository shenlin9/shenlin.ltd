---
title: chapter 4.1 - Git 协议
categories:
  - Git
  - Book-ProGit
tags:
  - Git
---

git 可以使用四种协议来传输数据：

    本地协议（Local Protocal）
    HTTP协议
    SSH协议（Secure SHell）
    Git协议

<!--more-->

## 本地协议

团队每一个成员都对一个共享的文件系统（如一个挂载的NFS）拥有访问权，这时远程版本库就是共享硬盘内的一个目录，使用版本库路径作为URL，例如克隆一个本地版本库：
```
$ git clone /opt/git/project.git
$ git clone file:///opt/git/project.git
```
注意上面的两种写法，是否带 `file://` git 行为会不同：
* 普通路径：git 会尝试使用硬链接或直接复制所需要的文件，通常这样较快。
* 通常使用普通路径。
* 开头明确指定 `file://` ：git 会触发用于网络传输资料的进程，通常传输效率较低。
* 指定 `file://` 的主要目的是取得一个没有外部参考（extraneous references）或对象（object）的干净版本库副本– 通常是在从其他版本控制系统导入后或一些类似情况（参见 Git 内部原理 for maintenance tasks）需要这么做

增加一个本地版本库到现有的 git 项目：
```
$ git remote add local_proj /opt/git/project.git
```

也可以快速从其他人的工作目录中拉取更新：
```
$ git pull /home/john/project
```
比推送到服务器再取回简单了多

### 优点

1. 如果已经存在共享文件系统，则建立版本库比较简单，可以直接使用现有的文件系统权限和网络访问权限

### 缺点

1. 配置不方便：共享文件系统比较难配置
2. 访问不方便：相比网络连接的访问方式，不方便从多个位置访问，如果想从家里访问，必须先挂载一个远程磁盘
3. 速度相对慢：使用共享挂载的文件系统访问不一定是最快的，在同一个服务器上，如果允许 git 访问本地硬盘，通过 NFS 访问版本库比通过 SSH 访问要慢。
4. 不能避免仓库被损坏：每一个用户都有远程目录的 shell 权限，没法阻止他们删除、修改、损坏 git 内部文件和仓库


## HTTP 协议

git 通过 HTTP 通信有两种模式：
git 1.6.6 之前的 dumb http（哑http）：通常是只读的
git 1.6.6 之后的 smart http（智能http）：可以读写，可智能的协商和传输数据

两种模式，git 客户端会优先使用 smart http，如果服务器不提供，则使用dumb http，通常都是两者选一，不会两种模式混用。

### 智能（Smart） HTTP 协议

* 运行在标准的 HTTP/S 端口上
* 可以使用 HTTP 的验证机制，如用户名/密码的基础授权或SSL证书等，比SSH 公钥的授权验证要简单
* 像 git:// 协议一样支持设置匿名服务
* 像 SSH 协议一样提供传输时的加密
* 使用起来很简单，只用一个 URL 就可以做到上述特性

### 哑（Dumb） HTTP 协议

* 设置起来更简单，只需要把一个裸版本库放在 HTTP 根目录，设置一个叫做 post-update 的挂钩就可以了
* web 服务器仅把裸版本库当作普通文件来对待，提供文件服务
```
//把裸版本库放入http根目录
$ cd /var/www/htdocs/
$ git clone --bare /path/to/git_project gitproject.git

//设置挂钩
$ cd gitproject.git
$ mv hooks/post-update.sample hooks/post-update

//设置访问权限
$ chmod a+x hooks/post-update
```
*  post-update 挂钩会默认执行合适的命令（git update-server-info），来确保通过 HTTP 的获取和克隆操作正常工作。 
*  在你通过 SSH 向版本库推送之后执行 `git update-server-info` 命令
*  然后其他人通过命令克隆：
```
$ git clone https://example.com/gitproject.git
```

### 优点

都是关于 smart http 的

* 终端用户使用 git 很简单，只需要一个 URL
* 用户名/密码授权比 SSH 密钥对有很大优势
    * 无需在使用 git 之前先在本地生成 SSH 密钥对再把公钥上传到服务器
* HTTP 协议的可用性比 SSH 更高
    * 对非资深使用者友好
    * 对缺少 SSH 相关程序的使用者友好
    * 企业防火墙都会允许 HTTP/S 端口的数据通过
* 和 SSH 协议一样快速和高效
* HTTPS 可以提供只读版本库的服务
* HTTPS 可以在传输时加密数据
* 可以指定客户端使用 SSL 证书

### 缺点

* 一些儿服务器上假设 HTTP/S 的服务端会比 SSH 棘手
* HTTP 上使用需授权的推送管理凭证会比 SSH 秘钥认证麻烦，可以使用凭证管理器

## SSH 协议

SSH 协议也是一个验证授权的网络协议

SSH 的 URL
```
$ git clone ssh://user@server:project.git
```
简短的 scp 写法
```
$ git clone user@server:project.git
```
也可以不指定用户名，则 git 使用当前登录的用户名

### 优点

* SSH 的架设相对简单
    * 多数操作系统都包含了 SSH 及相关工具 
    * 多数管理员都有使用经验
* SSH 的访问是安全的
    * 所有传输数据都要经过授权和加密
* SSH 的传输很高效
    * 在传输前会尽量压缩数据

### 缺点

* 不能实现匿名访问
    * 即使只是读取数据，也要有相应的权限 
    * 不利于开源项目
    * 适合只在公司网络内部使用

## Git 协议

git 协议是包含在 git 里的一个特殊的守护进程，监听一个特定的端口9418，访问无需任何授权

创建一个名为 git-daemon-export-ok 的文件放到 .git 目录下，表明该工程允许非授权访问，也是 Git 协议守护进程为这个版本库提供服务的必要条件
```
$ cd /path/to/project.git
$ touch git-daemon-export-ok
```
除此之外，git 协议无任何安全措施，要么谁都可以克隆这个库，要么谁都不能
```
$ git clone git://
```

### 优点

* 传输速度快
    * 是 git 使用的网络传输协议中最快的
    * 使用和 SSH 相同的数据传输机制，但是省去了加密和授权的开销
    * 适合很大访问量的项目
    * 适合项目庞大但不需要为写进行用户授权的项目

### 缺点

* 缺乏授权机制
    * 不适合作为版本库的唯一访问手段
    * 通常不能通过 git 协议推送，一旦开放推送操作，则所有知道项目 URL 的人都可以推送
    * 应同时提供 HTTPS 或 SSH 的访问服务，让少数开发者有推送权限，其他人通过 git:// 只有读取权限
* 最难架设
    * 需要有自己的守护进程，要配置xinetd或其他程序
    * 需要防火墙开放9418端口，企业防火墙一般不开放非标准端口
