---
title: Linux - 一些命令前加 sudo 执行时为什么提示 command not found
categories:
  - Linux
tags:
  - Linux
  - sudo
---

转载自：http://blog.csdn.net/poechant/article/details/7216892

用sudo来执行cd、ls等命令时，会出现command not found的提示：

<!--more-->

```bash
$ sudo cd /home/michael
sudo: command not found
```

我们知道在执行Linux命令时，如果在其前面加上sudo，就表示以root权限执行。但是这其实是有一个前提的，就是只有那些Linux内置系统命令才可以用如此的形式来执行，而对于Shell内置命令或其他用户自定义命令、别名等，是不能用sudo来使用root权限的。为什么呢？详细说一下sudo幕后隐藏的过程才能明白。

因为当在Linux下用sudo执行某一命令时，是在原进程（parent process）的基础上fork出来一个子进程（child process），这个子进程是以root权限执行的。然后在子进程中，执行你在sudo后面跟的命令。

在子进程中是无法调用涉及到父进程的状态的一些命令的，所以非系统内置命令会被拒绝。这就是为什么会出现command not found的提示。具体来说，当我们执行：

    sudo cd /home/michael  

所在这个shell进程中（称其为PP，表示parent process）fork出一个子进程（称其为CP，表示child process），那么在CP中是无法改变PP的所在目录的。

    sudo ls /home/michael  

sudo后由PP产生了CP，CP是无法获取PP所在的目录的内容的（具体来说，是读取该目录的block data，可详见《柳大的Linux游记·基础篇（2）Linux文件系统的inode》一文）。

## 解决方法

问题的原因: 在编译sudo包的时候默认开启了- -with-secure-path选项。

    方法1： 在/etc/sudoers文件内增加这么一行 `:Defaults secure_path=”/bin:/usr/bin:/usr/local/bin:…”`, 把要用的命令path包括进去。

    方法2： 用命令的绝对路径。

    方法3： 使用sudo的env选项,像这样sudo env PATH=$PATH cmd.sh。

    方法4： 把脚本拷贝或链接到系统$PATH中。

    方法5： 重新编译sudo,不带–with-secure-path选项了.(终极解决办法)。

## 解决方法

這是因為預裝的 sudo 在編譯時，--with-secure-path 採用了預設值，而導致原來 User 的 PATH 環境設置項，在 sudo 時被暫時覆蓋。
--with-secure-path 的詳盡說明如下:

    Sudo Installation Notes
    --with-secure-path[=PATH]
    Path used for every command run from sudo(8). If you don't trust the people running sudo to have a sane PATH environment variable you may want to use this. Another use is if you want to have the "root path" be separate from the "user path." You will need to customize the path for your site. NOTE: this is not applied to users in the group specified by --with-exemptgroup. If you do not specify a path, "/bin:/usr/ucb:/usr/bin:/usr/sbin:/sbin:/usr/etc:/etc" is used.

有個簡單的方法可以解決這個問題，只要做個 alias 便可以了。　　alias sudo=”sudo env PATH=$PATH”這個 alias 可以增加 env PATH=$PATH 敘述，使得每次 sudo 時，可以套上原有的 PATH 環境變數。當然，也可以透過 re-compile sudo package 而修改 secure path。

## 解决方法


    –with-secure-path[=PATH]

    Path used for every command run from sudo(8). If you don’t trust the people running sudo to have a sane PATH environment variable you may want to use this. Another use is if you want to have the “root path” be separate from the “user path.” You will need to customize the path for your site. NOTE: this is not applied to users in the group specified by –with-exemptgroup. If you do not specify a path, “/bin:/usr/ucb:/usr/bin:/usr/sbin:/sbin:/usr/etc:/etc” is used.


 知道了问题就好解决了，要么重新编译sudo，要么在环境配置文件里加一个alias

    alias sudo='sudo env PATH=$PATH'

## 解决方法

为了安全起见，/etc/sudoers 配置文件里 默认设置了sudo时，命令查找目录，所以你新加的命令，在配置完环境变量后也无法使用。默认配置：

    Defaults secure_path=/sbin:/bin:/usr/sbin:/usr/bin

简单的解决办法就是覆盖它：

在用户的主目录里的.bashrc中添加:

    alias sudo='sudo env PATH=$PATH'

修改 /etc/sudoers时，建议使用visudo命令来修改，该命令会验证输入是否正确

## 解决方法

sudo有时候会出现找不到命令，而明明PATH路径下包含该命令，让人疑惑。其实出现这种情况的原因，主要是因为当 sudo以管理权限执行命令的时候，linux将PATH环境变量进行了重置，当然这主要是因为系统安全的考虑，但却使得sudo搜索的路径不是我们想要的PATH变量的路径，当然就找不到我们想要的命令了。两种方法解决该问题：

首先，都要打开sudo的配置文件：sudo visudo

1.可以使用 secure_path 指令修改 sudoers 中默认的 PATH为你想要的路径。这个指令指定当用户执行 sudo 命令时在什么地方寻找二进制代码和命令。这个选项的目的显然是要限制用户运行 sudo 命令的范围，这是一种好做法。

2.将Defaults env_reset改成Defaults !env_reset取消掉对PATH变量的重置，然后在.bashrc中最后添加 `alias sudo='sudo env PATH=$PATH'`，这样sudo执行命令时所搜寻的路径就是系统的PATH变量中的路径，如想添加其他变量也是类似。
