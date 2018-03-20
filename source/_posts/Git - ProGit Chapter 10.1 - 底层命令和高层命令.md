---
title: chapter 10.1 - Git 底层命令和高层命令
categories:
  - Git
  - Book-ProGit
tags:
  - Git
---

Git 底层命令和高层命令

<!--more-->

## 本质概述

从根本上来讲 Git 是一个内容寻址（content-addressable）文件系统，并在此之上提供了
一个版本控制系统的用户界面，但它同时也是一个非常强大且易用的工具。

Git 是内容寻址文件系统，表示Git 的核心部分是一个简单的**键值对数据库（key-value
data store）**。

你可以向该数据库插入任意类型的内容，它会返回一个键值，通过该键值可以在任意时刻再
次检索（retrieve）该内容。 

## 底层命令和高层命令

Git 最初是一套面向版本控制系统的工具集，而不是一个完整的、用户友好的版本控制系统
，所以它还包含了一部分用于完成底层工作的命令。

这些命令被设计成以 UNIX 命令行的风格连接在一起，抑或藉由脚本调用，来完成工作。

这部分命令一般被称作“底层（plumbing）”命令，而那些更友好的命令则被称作“高层（
porcelain）”命令。

```
//查看索引文件
$ git ls-files -s

//合并冲突时查看索引中记录的冲突文件
$ git ls-files -u

//查看冲突文件的内容
$ git show :1:test.txt

//手工三方合并文件
$ git merge-file -p test.ours.txt test.common.txt test.theirs.txt > test.txt

//查看类型
$ git cat-file -t HEAD
commit

//查看内容
$ git cat-file -p HEAD
tree af8c345b595d21dc202d9673dd6d0f50c78ba4e1
parent f1c74ad1942ec4c77a3cd1cb34585b9e833271ec
author shenlin9 <bitbite@foxmail.com> 1501926645 +0800
committer shenlin9 <bitbite@foxmail.com> 1501926645 +0800

增加 Git打包 笔记


//反向解析获取 HEAD 的 SHA
$ git rev-parse HEAD
ac5a2498d263114d59685470d89e095af011cf46

//获取内容，和上面的一样
$ git cat-file -p ac5a2498d263114d59685470d89e095af011cf46
tree af8c345b595d21dc202d9673dd6d0f50c78ba4e1
parent f1c74ad1942ec4c77a3cd1cb34585b9e833271ec
author shenlin9 <bitbite@foxmail.com> 1501926645 +0800
committer shenlin9 <bitbite@foxmail.com> 1501926645 +0800

增加 Git打包 笔记


//查看树对象的文件列表
$ git cat-file -p af8c345b595d21dc202d9673dd6d0f50c78ba4e1
100644 blob 063b0e4ce79bbd23403f7e8ebfb71fb7779f869a    .gitignore
100644 blob adcee722021221c79611774c2df0b2b89548e504    .gitmodules
100644 blob 2a7ae0322c3770cde5cc5cbd8c01bcca2ccb0ddc    README.md
100644 blob dfb68d9133f43824e999af4b8be2a22fd304495d    _config.yml
100644 blob ce8a1edfece3d0e0ad08c30d38b386daf5274bd5    package.json
040000 tree fadf7ab7818d6053c708c5133a560cb3b4759281    scaffolds
040000 tree b0b001a9959f93704a3b95f516fe892f16d271c0    source
040000 tree 488f28d1e47c90cac01ccb1097d48b96aaf17d16    themes

//更直接简单的查看方式
$ git ls-tree HEAD
100644 blob 063b0e4ce79bbd23403f7e8ebfb71fb7779f869a    .gitignore
100644 blob adcee722021221c79611774c2df0b2b89548e504    .gitmodules
100644 blob 2a7ae0322c3770cde5cc5cbd8c01bcca2ccb0ddc    README.md
100644 blob dfb68d9133f43824e999af4b8be2a22fd304495d    _config.yml
100644 blob ce8a1edfece3d0e0ad08c30d38b386daf5274bd5    package.json
040000 tree fadf7ab7818d6053c708c5133a560cb3b4759281    scaffolds
040000 tree b0b001a9959f93704a3b95f516fe892f16d271c0    source
040000 tree 488f28d1e47c90cac01ccb1097d48b96aaf17d16    themes

```

## 关于名字

以前有个软件叫GNU Interactive Tools，简称也是GIT，于是git就只能叫git-core了。

但由于git名气实在太大，后来就把 GNU Interactive Tools 改成 gnuit，git-core 正式改为 git。

## 目录结构

`git init` 时，Git 会创建一个 .git 目录。 这个目录包含了几乎所有 Git 存储和操作的对象。 如若想备份或复制一个版本库，只需把这个目录拷贝至另一处即可。

`.git` 默认目录结构

```
$ ls -F1
HEAD
config*
description
hooks/
info/
objects/
refs/
```

Git 核心部分：
 
> 
HEAD 文件
index 文件（尚待创建）
objects 目录
refs 目录

Git 目录说明：

|目录|说明|
|-|-|
|description文件|仅供 GitWeb 程序使用|
|config文件|包含项目特有的配置选项|
|info目录|包含一个全局性排除（global exclude）文件，用以放置那些不希望被记录在 .gitignore 文件中的忽略模式（ignored patterns）|
|hooks目录|包含客户端或服务端的钩子脚本（hook scripts）|
|objects目录|存储所有数据内容|
|refs目录|存储指向数据（分支）的提交对象的指针|
|HEAD文件|指示目前被检出的分支|
|index文件|保存暂存区信息|


## 配置文件

### 全局配置文件

```
$ cat .git/config
[core]
        repositoryformatversion = 0
        filemode = false
        bare = false
        logallrefupdates = true
        symlinks = false
        ignorecase = true
[remote "origin"]
        url = git@github.com:bitbiteme/gitgo
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
        remote = origin
        merge = refs/heads/master
[branch "origin"]
        remote = .
        merge = refs/heads/dev
[branch "dev"]
[branch "dev"]
[branch "dev"]
        remote = origin
        merge = refs/heads/dev
[branch "dev-beta"]
        remote = origin
        merge = refs/heads/dev-beta
```
    
### 用户配置文件

```
$ cat ~/.gitconfig
[user]
        name = bitbite
        email = bitbite@foxmail.com
```

### 配置命令别名

可在配置文件增加`[alias]`，配置命令别名
--global参数表示命令在所有git仓库下都起作用

简化命令
```
$ git config --global alias.st status
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.br branch
```

设置unstage命令
```
$ git config --global alias.unstage 'reset HEAD'
```
则下面两条命令等价
```
$ git reset HEAD file2
$ git unstage file2
```

显示最后一条信息`git last`
```
$ git config --global alias.last 'log -1'
```

更改log信息显示样式
```
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

## 忽略文件

### PROJECT/.git/info/exclude 文件

用于放置不希望被放置在.gitignore文件中的忽略模式。

```
$ cat .git/info/exclude
# git ls-files --others --exclude-from=.git/info/exclude
# Lines that start with '#' are comments.
# For a project mostly in C, the following would be a good set of
# exclude patterns (uncomment them if you want to use them):
# *.[oa]
# *~
```

### PROJECT/.gitignore

工作区下的有些儿文件是需要的但又不适合加入git，如composer安装的依赖库等，则每次都会显示“Untracked files”
```
$ git status
On branch dev-beta
Your branch is up-to-date with 'origin/dev-beta'.
Untracked files:
  (use "git add <file>..." to include in what will be committed)

        4.txt
        5.txt
        6.txt
        vendor/

nothing added to commit but untracked files present (use "git add" to track)
```
解决此问题的方法是在 **项目** 根目录下建立.gitignore文件，把需要忽略的文件填进去
```
4.txt
vendor/
```

### 忽略文件的原则是：
1. 忽略自动生成的文件，包括操作系统生成的文件，如缩略图，编译生成的中间文件，目标文件，可执行文件
2. 忽略带有敏感信息的文件，如存放密码的配置文件等

### ignore相关网址

* 自动生成 ignore 文件
https://www.gitignore.io/
如生成phpstorm的ignore文件
```
# Created by https://www.gitignore.io/api/phpstorm

### PhpStorm ###
# Covers JetBrains IDEs: IntelliJ, RubyMine, PhpStorm, AppCode, PyCharm, CLion, Android Studio and Webstorm
# Reference: https://intellij-support.jetbrains.com/hc/en-us/articles/206544839

# User-specific stuff:
.idea/**/workspace.xml
.idea/**/tasks.xml
.idea/dictionaries

# Sensitive or high-churn files:
.idea/**/dataSources/
.idea/**/dataSources.ids
.idea/**/dataSources.xml
.idea/**/dataSources.local.xml
.idea/**/sqlDataSources.xml
.idea/**/dynamic.xml
.idea/**/uiDesigner.xml

# Gradle:
.idea/**/gradle.xml
.idea/**/libraries

# CMake
cmake-build-debug/

# Mongo Explorer plugin:
.idea/**/mongoSettings.xml

## File-based project format:
*.iws

## Plugin-specific files:

# IntelliJ
/out/

# mpeltonen/sbt-idea plugin
.idea_modules/

# JIRA plugin
atlassian-ide-plugin.xml

# Cursive Clojure plugin
.idea/replstate.xml

# Crashlytics plugin (for Android Studio and IntelliJ)
com_crashlytics_export_strings.xml
crashlytics.properties
crashlytics-build.properties
fabric.properties

### PhpStorm Patch ###
# Comment Reason: https://github.com/joeblau/gitignore.io/issues/186#issuecomment-215987721

# *.iml
# modules.xml
# .idea/misc.xml
# *.ipr

# Sonarlint plugin
.idea/sonarlint

# End of https://www.gitignore.io/api/phpstorm
```
* github上的 ignore 文件
https://github.com/github/gitignore
如laravel的.gitignore文件
```
vendor/
node_modules/
npm-debug.log

# Laravel 4 specific
bootstrap/compiled.php
app/storage/

# Laravel 5 & Lumen specific
public/storage
public/hot
storage/*.key
.env.*.php
.env.php
.env
Homestead.yaml
Homestead.json

# Rocketeer PHP task runner and deployment package. https://github.com/rocketeers/rocketeer
.rocketeer/
```
### 相关命令

检查.gitignore文件的书写是否有问题
```
$ git check-ignore -v .gitignore
```

加入.gitignore的文件不允许添加到库
```
$ echo 4.txt >> .gitignore

$ git add 4.txt
The following paths are ignored by one of your .gitignore files:
4.txt
Use -f if you really want to add them.
```
可以强制加入，不影响.gitigore文件
```
$ git add -f 4.txt

$ cat .gitignore
4.txt
```

## 本地分支和远程分支建立关联

//建立本地分支dev和远程分支的关联
`git branch --set-upstream-to=origin/<branch> dev`
~~git branch --set-upstream 被弃用~~
```
$ git branch --set-upstream-to=origin/dev dev
Branch dev set up to track remote branch dev from origin.
```
//建立关联后可直接push和pull
```
$ git pull
Already up-to-date.

$ git push
Everything up-to-date
```

//取消关联
```
git branch --unset-upstream
```

pull没有建立关联的方法
但push有建立关联方法
```
$ git push --set-upstream origin dev
Everything up-to-date
Branch dev set up to track remote branch dev from origin.
```

没有建立关联则push和pull需要指出远程分支和本地分支名
```
git push origin master
git pull origin master
```
 
--allow-unrelated-histories允许不同父目录分支的合并
```
git pull origin master --allow-unrelated-histories
```

本地分支和远程分支的名字不一致时，需要明确指明push到哪个分支
HEAD:dev指原来的远程库上的dev分支
dev-beat指在远程库上重新建立个和本地分支一样名称的dev-beta分支
```
$ git push
fatal: The upstream branch of your current branch does not match
the name of your current branch.  To push to the upstream branch
on the remote, use

    git push origin HEAD:dev

To push to the branch of the same name on the remote, use

    git push origin dev-beta

To choose either option permanently, see push.default in 'git help config'.

$ git push origin dev-beta
Total 0 (delta 0), reused 0 (delta 0)
To github.com:bitbiteme/gitgo
 * [new branch]      dev-beta -> dev-beta
```

远程库版本新导致push失败
```
$ git push origin HEAD:dev
To github.com:bitbiteme/gitgo
 ! [rejected]        HEAD -> dev (fetch first)
error: failed to push some refs to 'git@github.com:bitbiteme/gitgo'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
因为远程库的版本比本地版新，需要先pull下来进行合并，然后再push。如果合并时有冲突，则需要手工解决冲突，然后在本地分支里先add、commit之后再push。

## 查看远程库信息

```
$ git remote
origin

$ git remote -v
origin  git@github.com:bitbiteme/gitgo (fetch)
origin  git@github.com:bitbiteme/gitgo (push)
```

## 克隆远程库

使用clone命令克隆远程库
```
$ git clone git@github.com:bitbiteme/gitgo
Cloning into 'gitgo'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
```

克隆的库默认只有master分支，若需要dev分支，则：
```
$ git checkout -b dev-beta origin/dev
Switched to a new branch 'dev-beta'
Branch dev-beta set up to track remote branch dev from origin.
```
并且默认已建立关联

## 标签tag

git的版本号为40位长SHA值，标签则增加了许多便利。

查看tag列表
```
$ git tag
0.8
0.9
1.0
```

查看某个tag具体信息
```
$ git show 0.9
commit 856e7f30f208e1512d4e0a63257bfede1e2bedf7 (tag: 0.9, tag: 0.8)
Author: bitbite <bitbite@foxmail.com>
Date:   Sun Jun 11 11:21:31 2017 +0800

    append new line in branch dev

diff --git a/test.txt b/test.txt
index e538ede..d518d3e 100644
--- a/test.txt
+++ b/test.txt
@@ -1,3 +1,4 @@
 just a test file
 don't want say anything
 branch test
+append new line in branch dev
```

默认给当前最新的commit建立tag
```
$ git tag 1.0
```

给指定commit建立tag
```
$ git tag 1.0 fec145a
```

建立tag并增加说明
```
$ git tag -a 1.0 -m "1.0 online now"
```

建立tag使用PGP签名
```
$ git tag -s 1.0 -m "signed version 1.0 released"
```

推送指定tag和全部tag
git的push命令默认不推送tag，需要额外推送
```
$ git push origin 1.0
Total 0 (delta 0), reused 0 (delta 0)
To github.com:bitbiteme/gitgo
 * [new tag]         1.0 -> 1.0

$ git push origin --tags
Total 0 (delta 0), reused 0 (delta 0)
To github.com:bitbiteme/gitgo
 * [new tag]         0.8 -> 0.8
 * [new tag]         0.9 -> 0.9
```

删除tag
```
$ git tag -d 0.7
Deleted tag '0.7' (was 89086a5)
```

删除远程分支tag
```
$ git push origin :refs/tags/0.8
To github.com:bitbiteme/gitgo
 - [deleted]         0.8
```

## config

```git
git config -l
git config --global user.name "bitbite"
git config --global user.email "bitbite@foxmail.com"

$ git init
Initialized empty Git repository in D:/learngit/.git/

$ git add readme.txt

$ git commit -m 'wrote a readme file'
[master (root-commit) f503d8b] wrote a readme file
 1 file changed, 2 insertions(+)
 create mode 100644 readme.txt
 
$ vim readme.txt

$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")


$ git diff
diff --git a/readme.txt b/readme.txt
index db83a04..2fdf0c4 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is version control system
+Git is a distributed version control system
 Git is free software

$ git add readme.txt

$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   readme.txt
        
$ git commit -m "add distributed"
[master 6ffe09e] add distributed
 1 file changed, 1 insertion(+), 1 deletion(-)

$ git status
On branch master
nothing to commit, working tree clean

$ git log
commit ac4b8d9ce8d56be4791e322c2802e9d70bfe2503 (HEAD -> master)
Author: bitbite <bitbite@foxmail.com>
Date:   Sun Jun 11 06:35:35 2017 +0800

    modified 2.txt and write some words

commit f9441dcc0e6b382396cc0e4c7a09a32d59d628a6
Author: bitbite <bitbite@foxmail.com>
Date:   Sun Jun 11 06:27:08 2017 +0800

    add 1.txt 2.txt and modified redmetxt

commit 6ffe09e1d1ce8226d2a54f7b0493fac5f46edf64
Author: bitbite <bitbite@foxmail.com>
Date:   Sat Jun 10 22:39:15 2017 +0800

    add distributed

commit f503d8b4a8be20a351e8b35ee1ea7ed574236a8d
Author: bitbite <bitbite@foxmail.com>
Date:   Sat Jun 10 22:26:13 2017 +0800

    wrote a readme file



$ git reset --hard HEAD^
HEAD is now at f9441dc add 1.txt 2.txt and modified redmetxt

$ git log
commit f9441dcc0e6b382396cc0e4c7a09a32d59d628a6 (HEAD -> master)
Author: bitbite <bitbite@foxmail.com>
Date:   Sun Jun 11 06:27:08 2017 +0800

    add 1.txt 2.txt and modified redmetxt

commit 6ffe09e1d1ce8226d2a54f7b0493fac5f46edf64
Author: bitbite <bitbite@foxmail.com>
Date:   Sat Jun 10 22:39:15 2017 +0800

    add distributed

commit f503d8b4a8be20a351e8b35ee1ea7ed574236a8d
Author: bitbite <bitbite@foxmail.com>
Date:   Sat Jun 10 22:26:13 2017 +0800

    wrote a readme file

$ cat 2.txt

$ git reset --hard ac4b8d9
HEAD is now at ac4b8d9 modified 2.txt and write some words

$ cat 2.txt
let us do sth in this txt file number 2

$ git reflog
ac4b8d9 (HEAD -> master) HEAD@{0}: reset: moving to ac4b8d9
f9441dc HEAD@{1}: reset: moving to HEAD^
ac4b8d9 (HEAD -> master) HEAD@{2}: commit: modified 2.txt and write some words
f9441dc HEAD@{3}: commit: add 1.txt 2.txt and modified redmetxt
6ffe09e HEAD@{4}: commit: add distributed
f503d8b HEAD@{5}: commit (initial): wrote a readme file

$ echo new line again >> 3.txt

ssl@sslnotebook MINGW32 /d/learngit (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   3.txt

no changes added to commit (use "git add" and/or "git commit -a")

$ cat 3.txt
3.txt modified
new line
stage line
new line again

$ git checkout -- 3.txt

$ git status
On branch master
nothing to commit, working tree clean

$ cat 3.txt
3.txt modified
new line
stage line

$ echo new line come again >> 3.txt

$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   3.txt

no changes added to commit (use "git add" and/or "git commit -a")

$ git add 3.txt

$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   3.txt


$ git reset HEAD 3.txt
Unstaged changes after reset:
M       3.txt

$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   3.txt

no changes added to commit (use "git add" and/or "git commit -a")


```

git add 的各种区别:

git add -A   // 添加所有改动

git add *     // 添加新建文件和修改，但是不包括删除

git add .    // 添加新建文件和修改，但是不包括删除

git add -u   // 添加修改和删除，但是不包括新建文件
在 commit 前撤销 add:

git reset <file> // 撤销提交单独文件

git reset        // unstage all due changes
add/commit 前撤销对文件的修改:

git checkout -- README.md  // 注意, add添加后(同commit提交后)就无法通过这种方式撤销修改

git reset 2.txt  //从stage删除

git log 2.txt

git log

在Git中，用HEAD表示当前版本，上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100

Git的版本回退速度非常快，因为Git在内部有个指向当前版本的HEAD指针，当你回退版本的时候，Git仅仅是把HEAD从指向

git diff            #是工作区(working directory)和暂存区(stage)的比较
git diff --cached    #是暂存区(stage)和分支(master)的比较
git diff HEAD       #工作区和版本库的比较
git diff HEAD -- 3.txt

Git的版本回退速度非常快，因为Git在内部有个指向当前版本的HEAD指针，当你回退版本的时候，Git仅仅是把HEAD从指向


git checkout -- file1 直接丢弃工作区的修改，从stage或branch取回最近的版本
git reset HEAD file1  unstage file1

git rm file1
相当于
rm file1
git add file1


## 合并两个没有共同上级的分支

```
$ git pull origin master
From github.com:bitbiteme/hello-world
 * branch            master     -> FETCH_HEAD
fatal: refusing to merge unrelated histories

$ git pull origin master --allow-unrelated-histories
From github.com:bitbiteme/hello-world
 * branch            master     -> FETCH_HEAD
Merge made by the 'recursive' strategy.
 README.md | 6 ++++++
 1 file changed, 6 insertions(+)
 create mode 100644 README.md

ssl@sslnotebook MINGW32 /d/learngit (master)
$ git pull origin master --allow-unrelated-histories
From github.com:bitbiteme/hello-world
 * branch            master     -> FETCH_HEAD
Already up-to-date.

$ git status
On branch master
nothing to commit, working tree clean

$ git push -u origin master
Counting objects: 27, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (21/21), done.
Writing objects: 100% (27/27), 2.50 KiB | 0 bytes/s, done.
Total 27 (delta 3), reused 0 (delta 0)
remote: Resolving deltas: 100% (3/3), done.
To github.com:bitbiteme/hello-world.git
   f3b43b0..415940c  master -> master
Branch master set up to track remote branch master from origin.

$ git push origin master
Everything up-to-date

$ git log
commit 415940c73b228cb01a408b55ea2c0b5127f17a1a (HEAD -> master, origin/master)
Merge: ce0daa8 f3b43b0
Author: bitbite <bitbite@foxmail.com>
Date:   Sun Jun 11 10:14:36 2017 +0800

    Merge branch 'master' of github.com:bitbiteme/hello-world

$ git reflog
415940c (HEAD -> master, origin/master) HEAD@{0}: pull origin master --allow-unrelated-histories: Merge made by the 'recursive' strategy.

```


## 分支管理

### 创建分支

创建dev分支
```
git checkout -b dev

//等同于下面两条命令
git branch dev
git checkout dev

//切换到master分支
git checkout master
//合并dev分支到master
git merge dev
//删除dev分支
git branch -d dev
//查看分支列表
git branch

//有冲突时无法切换分支
$ git checkout dev
error: you need to resolve your current index first
test.txt: needs merge

//没有提交时也无法切换分支
$ git checkout dev
Switched to branch 'dev'

$ vi test.txt

$ git checkout master
error: Your local changes to the following files would be overwritten by checkout:
        test.txt
Please commit your changes or stash them before you switch branches.
Aborting

//查看合并和commit的图形表示，快捷键b上翻f下翻q退出h帮助
$ git log --graph
*   commit a11aed78491dff564a79a3a49b6e24d33dfc487d (HEAD -> master)
|\  Merge: 59d4a43 4a1c5c6
| | Author: bitbite <bitbite@foxmail.com>
| | Date:   Sun Jun 11 11:40:35 2017 +0800
| |
| |     merge user master version
| |
| * commit 4a1c5c6738d89357afdc145664498e9647cfbdd3 (dev)
| | Author: bitbite <bitbite@foxmail.com>
| | Date:   Sun Jun 11 11:35:21 2017 +0800
| |
| |     modified branch dev again
| |
* | commit 59d4a43b655068c0ae8d5d0d1408e8f9e35583fc
| | Author: bitbite <bitbite@foxmail.com>
| | Date:   Sun Jun 11 11:37:27 2017 +0800
| |
| |     modified not the same lien in branch master
| |
* |   commit 7ede7d8ec9b59b6fb4bec2cf90b0de6568d951b3 (origin/master, origin/HEAD)
|\ \  Merge: 2933a4a 856e7f3
| |/  Author: bitbite <bitbite@foxmail.com>
| |   Date:   Sun Jun 11 11:29:45 2017 +0800
| |
| |       resolve the  conflict
| |
| * commit 856e7f30f208e1512d4e0a63257bfede1e2bedf7
| | Author: bitbite <bitbite@foxmail.com>
| | Date:   Sun Jun 11 11:21:31 2017 +0800
| |
| |     append new line in branch dev
| |
* | commit 2933a4a539e449398e779fa847614edb4a71e782
|/  Author: bitbite <bitbite@foxmail.com>
|   Date:   Sun Jun 11 11:23:00 2017 +0800
|
|       append new line in branch master
|


//查看合并分支的图形表示 --abbrev-commit表示缩写SHA版本号
$ git log --graph --pretty=oneline --abbrev-commit
*   a11aed7 (HEAD -> master) merge user master version
|\
| * 4a1c5c6 (dev) modified branch dev again
* | 59d4a43 modified not the same lien in branch master
* |   7ede7d8 (origin/master, origin/HEAD) resolve the  conflict
|\ \
| |/
| * 856e7f3 append new line in branch dev
* | 2933a4a append new line in branch master
|/
* c73e52f append branch test content
* cfcec50 append one line on the github.com throught the net
* a69642a create test.txt and one line content
* f26fdbd Initial commit


```

查看分支 git branch
新建分支 git branch branch-name
删除分支 git branch -d branch-name
删除未被合并过的分支  git branch -D branch-name
合并分支 git merge branch-name  将branch-name分支合并到当前分支
切换分支 git checkout branch-name
新建并切换到分支 git checkout -b branch-name
查看合并的图形
```
$ git log --graph
$ git log --graph --pretty=oneline --abbrev-commit
```
#### 强制禁用 fast forward 模式

通常，合并分支时，如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

使用参数 --no-ff 强制禁用 fast forward 模式
```
$ git merge --no-ff -m "merge with no-ff" dev
```
#### 把工作区用 stash 存放起来

如果需要另开一个分支进行其他工作，如修复bug等，但开发还未完成一个阶段没有提交，这时切换分支被禁止，可以使用 stash 功能把工作区存放起来，修复完 bug 后再把存放的工作区恢复。

没有提交不允许切换分支
```
$ git checkout dev
error: Your local changes to the following files would be overwritten by checkout:
        test.txt
Please commit your changes or stash them before you switch branches.
Aborting
```
把工作区存放起来
```
$ git stash
Saved working directory and index state WIP on master: a11aed7 merge user master version

```
然后工作区就clean了
```
$ git status
On branch master
Your branch is ahead of 'origin/master' by 3 commits.
  (use "git push" to publish your local commits)
nothing to commit, working tree clean

```
查看存放的列表
```
$ git stash list
stash@{0}: WIP on master: a11aed7 merge user master version

```
新建修复bug分支
```
$ git checkout -b issue-101
Switched to a new branch 'issue-101'
//do some modify and commit...

```
修复完后切回原分支
```
$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 3 commits.
  (use "git push" to publish your local commits)

```
把存放的工作区恢复
```
$ git stash apply
On branch master
Your branch is ahead of 'origin/master' by 3 commits.
  (use "git push" to publish your local commits)
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   test.txt

```
并没有自动删除存放
```
$ git stash list
stash@{0}: WIP on master: a11aed7 merge user master version

```
删除存放的工作区
```
$ git stash drop
Dropped refs/stash@{0} (024c6a4ab4410ef0154d769f75c03f32ea88a5d5)

```
git stash
git stash list
git stash apply
git stash drop
git stash pop  等同于先 apply 再 drop


### 分支策略

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；

你和你的小伙伴们每个人都在dev分支上干活，每个人又有个人自己的分支，时不时地往dev分支上合并就可以了。

bug修复时应该建立独立的分支，如issue-101，修复完毕后再合并到dev分支

新功能应该建立feature分支

1. master分支
2. dev分支
3. 成员个人分支
4. bug分支
5. feature分支

## 搭建git服务器








