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