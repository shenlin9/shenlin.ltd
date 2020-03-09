---
title: Linux - XDG 用户目录 - nvim,vim
categories:
- Linux
tags:
- Linux
- nvim
- vim
date: 2020-02-28 15:52:56
---

Linux - XDG 用户目录 - nvim,vim

<!--more-->

https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html

## XDG 基本目录规范

遵守 XDG 目录规范的程序会读取系统环境变量，然后根据系统环境变量的值指定的存储目
录存储对应的文件，XDG 环境变量和含义如下，注意所有路径都必须为绝对路径：

**$XDG_DATA_HOME**

用户数据文件目录

**$XDG_CONFIG_HOME**

用户配置文件目录

**$XDG_DATA_DIRS**

搜索用户数据文件的一组目录，有优先顺序

**$XDG_CONFIG_DIRS**

搜索用户配置文件的一组目录，有优先顺序

**$XDG_CACHE_HOME**

用户缓存文件目录

**$XDG_RUNTIME_DIR**

用户运行时文件和特定的文件对象目录

## Windows 设置环境变量

Windows 下可在系统属性--高级--环境变量中设置，或使用 SETX 命令创建环境变量：
```
SETX variable_name value /m
```
如：
```
SETX XDG_CONFIG_HOME C:\Users\foo\bar
```

## Vim 设置

neovim 和 vim 都可以使用 XDG 目录， vim 的设置：
```vim
" Environment
set directory=$XDG_CACHE_HOME/vim,~/,/tmp
set backupdir=$XDG_CACHE_HOME/vim,~/,/tmp
set viminfo+=n$XDG_CACHE_HOME/vim/viminfo
set runtimepath=$XDG_CONFIG_HOME/vim,$XDG_CONFIG_HOME/vim/after,$VIM,$VIMRUNTIME
let $MYVIMRC="$XDG_CONFIG_HOME/vim/vimrc"

export VIMINIT='let $MYVIMRC="$XDG_CONFIG_HOME/vim/vimrc" | source $MYVIMRC'
```

