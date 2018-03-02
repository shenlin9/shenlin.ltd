---
title: Git Commands List
categories: Git
tags:
  - Git
  - Git-Commands
---

Git 常用命令列表

<!--more-->

## 安装

### 最小化安装 git 依赖包
```
$ sudo yum install curl-devel expat-devel gettext-devel \
           openssl-devel zlib-devel
```
### 安装文档格式（如 doc, html, info）依赖包
```
  $ sudo yum install asciidoc xmlto docbook2x
  $ sudo apt-get install asciidoc xmlto docbook2x
```
### 编译安装 git
```
$ tar -zxf git-2.0.0.tar.gz
$ cd git-2.0.0
$ make configure
$ ./configure --prefix=/usr
$ make all doc info
$ sudo make install install-doc install-html install-info
```

## 配置

### 设置用户名、邮箱
```
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```
### 设置文本编辑器
```
$ git config --global core.editor vim
```
### 设置命令别名
```
$ git config --global alias.unstage 'reset HEAD --'
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
//执行外部命令，在命令前面加`!`
$ git config --global alias.visual '!gitk'
```
### 查看配置文件
```
//系统配置文件
$ git config --system --list

//用户配置文件
$ git config --global --list

//当前库配置文件
$ git config --local --list

//所有配置信息，会有重复项目
$ git config --list
```
### 检查某一项设置
```
$ git config user.name
```
### 寻求帮助
```
//显示可用选项
$ git config -h

//打开命令手册
$ git config --help
$ git help config

//打开man帮助文件
$ man git-config
```



