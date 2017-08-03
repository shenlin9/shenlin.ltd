# tmp

tags ：AAA Book-ProGit

---

git config --global merge.tool meld
git config --global mergetool.meld.path "/c/Program Files (x86)/Meld/Meld.exe"

git checkout --ours file 和 git checkout --theirs file


    如果合并时发生冲突了，使用下面的命令回到合并之前的状态？
git reset --hard


$ git merge hotfix
fatal: You have not concluded your merge (MERGE_HEAD exists).
Please, commit your changes before you merge.


```
git@e42.matrix git
>> sudo mkdir project.git

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for git: 
git 不在 sudoers 文件中。此事将被报告。
```


```
>> git status
# On branch master
#
# Initial commit
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	composer.json
#	composer.lock
#	installer
#	slim/
#	test.php
#	vendor/
nothing added to commit but untracked files present (use "git add" to track)

ssy@e42.matrix testdir
>> git add .

ssy@e42.matrix testdir
>> git status
# On branch master
#
# Initial commit
#
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#
#	new file:   composer.json
#	new file:   composer.lock
#	new file:   installer
#	new file:   slim
#	new file:   test.php
#	new file:   vendor/autoload.php

>> git rm --cached .
fatal: not removing '.' recursively without -r

>> git rm --cached -r .
rm 'composer.json'
rm 'composer.lock'
rm 'installer'
rm 'slim'
rm 'test.php'
```


```
//查看磁盘使用情况
>> df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root
                      3.0G  2.8G  115M  97% /
tmpfs                 250M     0  250M   0% /dev/shm
/dev/sda1             477M   26M  426M   6% /boot

//查看已安装的kernel
>> rpm -qa|grep kernel
kernel-2.6.32-696.el6.i686
kernel-headers-2.6.32-696.1.1.el6.i686
kernel-firmware-2.6.32-696.el6.noarch
dracut-kernel-004-409.el6_8.2.noarch

//查看当前kernel
>> uname -r
2.6.32-696.el6.i686

//删除多余kernel
>> yum remove kernel-firmwar-2.23434

//查看目录大小
>> sudo du -hs /var/log
3.4M	/var/log

//查找大文件
>> find / -size +1G
```

设置 meld 为默认比较工具

* git 提供了一个工具列表，使用在列表之外的其他比较工具会稍微麻烦些儿

* 使用列表中的 meld 步骤则如下

```
git config --global diff.tool meld
git config --global merge.tool meld
git config --global difftool.prompt false
git config --global difftool.prompt false //这一项不知道是否有，还需要测试，difftool 是有的

git difftool
git mergetool -y //-y参数是不询问
```

这个 external 是不是老版本被放弃了，需要搜索确认
```
git config --global diff.external meld
```

比较文件时，使用`git diff`和`git difftool`有区别吗？

```

$ git difftool

直接启动了 meld 比较文件，貌似没问题
```


关于 meld 多穿参数的解决方案，貌似是 ubuntu 的，不知 windows 和 redhat 是否一样

看提示貌似和设置为 external 有关
```
$ git diff
Usage:
  meld                              Start with an empty window
  meld <file|folder>                Start a version control comparison
  meld <file> <file> [<file>]       Start a 2- or 3-way file comparison
  meld <folder> <folder> [<folder>] Start a 2- or 3-way folder comparison

Error: too many arguments (wanted 0-3, got 7)

fatal: external diff died, stopping at source/_posts/chapter 7.11 - Git 子模块.md
```
-----------------------------------------

这时，虽然Meld会被调用，但是系统会报错”Wrong number of arguments(Got 7)”。

原因是GIT会送7个参数给Meld，但是Meld只需要两个参数，两个需要比较的文件名。所以不能直接用Meld，必需要做一点小修改：

在自己的目录下建立一个git-meld.sh的script

```
vim ~/git-meld.sh
```

加入以下内容：

```
#!/bin/sh meld $2 $5
```

更改文件的权限：

```
chmod 777 ~/git-meld.sh
```

然后把external diff改成这个shell script：

```
git config --global diff.external ~/git-meld.sh
```
------------------------------


How to: Meld for Git diffs in Ubuntu Hardy


Are you using Git in Ubuntu and want to use an external visual diff viewer? It's easy! I will be using Meld for this example but most visual diff tools should be similar. If you don't already have Meld, then install it:

```
sudo apt-get install meld
```

Ok. Now let's begin by breaking it. Enter this into a terminal:

```
git config --global diff.external meld
```

Then navigate to a Git tracked directory and enter this (with an actual filename):

```
git diff filename
```

Meld will open but it will complain about bad parameters. The problem is that Git sends its external diff viewer seven parameters when Meld only needs two of them; two filenames of files to compare. One way to fix it is to write a script to format the parameters before sending them to Meld. Let's do that.

Create a new python script in your home directory (or wherever, it doesn't matter) and call it diff.py.

```
#!/usr/bin/python

import sys
import os

os.system('meld "%s" "%s"' % (sys.argv[2], sys.argv[5]))
```

Now we can set Git to perform it's diff on our new script (replacing the path with yours):

```
git config --global diff.external /home/nathan/diff.py
git diff filename
```

This time when we do a diff it will launch Meld with the right parameters and we will see our visual diff.

If I just made your day a little better then thank me with a coffee:

---

    git bash 窗口上点鼠标右键--> options --> text --> local 设置为 zh_ch
                                                    text 设置为 GBK

重启 git bash 窗口即可

-----


    					                ________________
    					                |
    					                |	HEAD
    					                |	 |
    [working directory] 	[Index]		|	master
    					                |	 |
    					                |	93a378d
    					                |	filev3
    					                |______________

---

$ git ls-remote https://github.com/shenlin9/shenlin.ltd
fatal: unable to access 'https://github.com/shenlin9/shenlin.ltd/': error setting certificate verify locations:
  CAfile: C:/Program Files/Git/mingw32/ssl/certs/ca-bundle.crt
  CApath: none
