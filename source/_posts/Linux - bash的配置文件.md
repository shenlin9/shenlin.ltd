---
title: linux - Bash 的配置文件
categories:
  - Linux
  - Bash
tags:
  - Linux
  - Bash
---

Bash 的配置文件

<!--more-->

交互式shell
:    shell接受来自键盘的命令

非交互式shell
:    shell的输入来自文件，如执行shell脚本

# 交互式Shell

用户登录后的默认shell定义在：
> /etc/passwd

## 登录前

在用户获得控制权之前，bashell会顺序运行下列文件：
> 
1. /etc/profile
    /etc目录下的文件会因为系统更新而被更改，所以不要直接编辑这里的文件
2. /etc/profile.d/*.sh
    /etc/profile.d目录下的所有sh文件都会被执行，所以为所有用户定义shell环境，可以在这里新建.sh文件
3. 在用户家目录下，按顺序查找这3个文件，并运行找到的第1个文件：
    3.1 ~/.bash_profile
    3.2 ~/.bash_login
    3.3 ~/.profile
    可以在上面的文件里定义个人的shell环境

## 登录后

在用户登录后，bashell对于交互式shell会运行文件：
> ~/.bashrc

bashell关于配置文件的选项：
> 
    --login     强制bashell读取配置文件
    --noprofile 跳过配置文件
    --norc      禁止对交互式shell执行~/.bashrc文件
    --rcfile    强制使用指定的~/.bashrc之外的文件

~/.bash_profile中有对.bashrc文件的调用

## 注销时

在用户注销时，bashell会运行：
> ~/.bash_logout

# 非交互式shell

运行脚本时，bash会在非交互式shell中运行，这时不会读取配置文件，但如果设置了BASH_ENV变量，bash会扩展该值并假设它是一个文件名称，如果文件存在，则会在运行脚本之前运行此文件。




