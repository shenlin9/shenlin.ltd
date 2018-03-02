---
title: chapter 3.3 - Git 远程分支
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-Remote
---

远程仓库是指托管在因特网或其他网络中的你的项目的版本库。

你可以有好几个远程仓库，通常有些仓库对你只读，有些则可以读写。

<!--more-->

### 添加远程库

命令：`git remote add <shortname> <url>`

shorname 是远程库的简写
```
$ git remote add pb https://github.com/paulboone/ticgit
```

### 查看远程库

```
//联网查看远程库的完整**引用**列表
$ git ls-remote
From git@github.com:bitbiteme/gitgo.git
c4b5281e4843ee6dfcdeb3af281e5842104ca8d2        HEAD
a3b00c3d3d5d23789d258747e4e4685ce6e15fa6        refs/heads/dev
a3b00c3d3d5d23789d258747e4e4685ce6e15fa6        refs/heads/dev-beta
c4b5281e4843ee6dfcdeb3af281e5842104ca8d2        refs/heads/master
d0135c011d58a1081cc944b05fecc9fa8398a8ea        refs/heads/monster
856e7f30f208e1512d4e0a63257bfede1e2bedf7        refs/tags/0.9
4a1c5c6738d89357afdc145664498e9647cfbdd3        refs/tags/1.0

//联网查看远程库 origin 的 heads 列表
$ git ls-remote --heads origin
a3b00c3d3d5d23789d258747e4e4685ce6e15fa6        refs/heads/dev
a3b00c3d3d5d23789d258747e4e4685ce6e15fa6        refs/heads/dev-beta
c4b5281e4843ee6dfcdeb3af281e5842104ca8d2        refs/heads/master
d0135c011d58a1081cc944b05fecc9fa8398a8ea        refs/heads/monster

//查看本地已添加的远程库，只显示简写
$ git remote
origin

//查看本地已添加的远程库，显示远程库简写及对应url
$ git remote -v
origin	https://github.com/schacon/ticgit (fetch)
origin	https://github.com/schacon/ticgit (push)
pb	https://github.com/paulboone/ticgit (fetch)
pb	https://github.com/paulboone/ticgit (push)

//查看某个远程库的详细信息
//会显示远程库和本地库的对应关系
$ git remote show origin
* remote origin
  URL: https://github.com/my-org/complex-project
  Fetch URL: https://github.com/my-org/complex-project
  Push  URL: https://github.com/my-org/complex-project
  HEAD branch: master
  Remote branches:
    master                           tracked
    dev-branch                       tracked
    markdown-strip                   tracked
    issue-43                         new (next fetch will store in remotes/origin)
    issue-45                         new (next fetch will store in remotes/origin)
    refs/remotes/origin/issue-11     stale (use 'git remote prune' to remove)
  Local branches configured for 'git pull':
    dev-branch merges with remote dev-branch
    master     merges with remote master
  Local refs configured for 'git push':
    dev-branch                     pushes to dev-branch                     (up to date)
    markdown-strip                 pushes to markdown-strip                 (up to date)
    master                         pushes to master                         (up to date)
```
最后一个命令
列出了在特定的分支上执行 git push 会自动地推送到哪一个远程分支
列出了哪些远程分支不在你的本地，
哪些远程分支已经从服务器上移除了，
还有当执行 git pull 时哪些分支会自动合并。


### 重命名远程库

```
$ git remote rename pb paul
```

### 移除远程库

```
$ git remote rm paul
```

## 远程/引用/分支/跟踪

* 远程引用是远程仓库上的引用（指针），包括分支、标签等等。

* 远程分支是远程仓库上的普通分支。

* 远程跟踪分支是远程分支状态的引用。 
 * 以 `(remote)/(branch)` 形式命名。
 * 它们是你不能移动的本地引用，当你做任何网络通信操作时，它们会自动移动。 
 * 远程跟踪分支像是你上次连接到远程仓库时，那些分支所处状态的书签。

* 跟踪分支
 * 本地分支与远程分支建立直接关系后就成为跟踪分支，
 * 如从一个远程跟踪分支检出一个本地分支就自动创建了一个 “跟踪分支”
 * 而上例中被跟踪的远程跟踪分支称为上游分支。
 
### 远程分支、远程跟踪分支、跟踪分支的工作流程

![](https://git-scm.com/book/en/v2/images/remote-branches-1.png)

* 有远程库 git.ourcompany.com，远程分支 master
* 使用克隆功能拉取全部数据到本地 My Computer
* 默认在本地建立远程库引用origin，远程跟踪分支 origin/master
* 建立本地分支 master，也指向 origin/master 所指向的提交

![](https://git-scm.com/book/en/v2/images/remote-branches-2.png)

* 服务器 git.ourcompany.com 上的远程分支 master 有人进行了提交更新，master 指针移动了
* 本地计算机 My Computer 上 master 分支也进行了提交更新，master 指针也移动了
* 本地计算机 My Computer 还未 fetch 远程库的数据，所以远程跟踪分支 origin/master 指针没有移动

![](https://git-scm.com/book/en/v2/images/remote-branches-3.png)

* 本地计算机 My Computer 去 fetch 远程库的数据
* 用远程跟踪分支 origin/master 去 merge 取回的数据，origin/master 指针这时才发生移动
* 本地和远程在共同的分支基础上分别进行了开发，merge 后自然本地和远程分叉了

![](https://git-scm.com/book/en/v2/images/remote-branches-4.png)

* 添加了新的远程库teamone git.team1.ourcompany.com

![](https://git-scm.com/book/en/v2/images/remote-branches-5.png)

* 新远程库 teamone 的数据是原远程库 origin 数据的子集
* 所以 git 不会拉取新远程库 teamone 数据
* 而是直接创建远程跟踪分支 teamone/master，并指向提交对象

### 抓取(fetch)和拉取(pull)

```
$ git fetch pb
```
git fetch 命令会将数据拉取到你的本地仓库 - 它并不会自动合并或修改你当前的工作。 当准备好时你必须手动将其合并入你的工作。
```
$ git merge origin/master
```

`$ git pull`的含义是`$ git fetch; git merge`
如果有一个分支设置为跟踪一个远程分支，可以使用 git pull 命令来自动的抓取并自动尝试合并到当前所在的分支

由于 git pull 的魔法经常令人困惑所以通常单独显式地使用 fetch 与 merge 命令会更好一些。

### 推送到远程库

本地分支不会与远程库自动同步，必须手动推送本地分支。
```
//推送本地的 master 分支来更新远程库 origin 上的 master 分支
//都会被扩展为  refs/heads/master:refs/heads/master
$ git push origin master
$ git push origin master:master

//推送本地的 master 分支来更新远程库 origin 上的 newmaster 分支
$ git push origin master:newmaster
```

### 跟踪分支

跟踪分支是与远程分支有直接关系的本地分支，远程分支也称为上游分支。

#### 创建跟踪分支

当克隆一个仓库时，通常会自动地创建一个跟踪 origin/master 的 master 分支
```
$ git clone git@github.com:bitbite/gito.git
```

从一个远程跟踪分支检出一个本地分支会自动创建一个叫做 “跟踪分支”
```
$ git checkout -b dev origin/dev
$ git checkout -b newBranchName origin/dev
```
track 是上面操作的快捷方式
```
$ git checkout --track origin/dev
```

设置已有的本地分支跟踪一个刚刚拉取下来的远程分支
或者修改正在跟踪的上游分支
```
$ git branch -u origin/dev

$ git branch -u origin/master

    Branch master set up to track remote branch master from origin.

$ git push -u origin master
```

可以使用`@{u}`来表示上游分支，下面两个命令等效
```
$ git merge origin/master
$ git merge @{u}

$ git merge origin/master --allow-unrelated-histories
```

#### 查看跟踪分支

查看所有跟踪分支
```
$ git branch -vv
  iss53     7e424c3 [origin/iss53: ahead 2] forgot the brackets
  master    1ae2a45 [origin/master] deploying index fix
* serverfix f8674d9 [teamone/server-fix-good: ahead 3, behind 1] this should do it
  testing   5ea463a trying something new
```
* iss53 分支正在跟踪 origin/iss53 并且 “ahead” 是 2，意味着本地有两个提交还没有推送到服务器上
* master 分支正在跟踪 origin/master 分支并且是最新的
* serverfix 分支正在跟踪 teamone 服务器上的 server-fix-good 分支并且领先 2 落后 1，意味着服务器上有一次提交还没有合并入同时本地有三次提交还没有推送
* 最后看到 testing 分支并没有跟踪任何远程分支

注意：ahead 3,behind 1 这些数字的值来自于你从每个服务器上最后一次抓取的数据。 这个命令并没有连接服务器，它只会告诉你关于本地缓存的服务器数据

统计最新的领先与落后数字，需要在运行此命令前抓取所有的远程仓库。 可以像这样做：
```
$ git fetch --all; git branch -vv
```

### 删除远程分支

```
$ git push origin --delete dev
```
基本上这个命令做的只是从服务器上移除这个指针。
Git 服务器通常会保留数据一段时间直到垃圾回收运行，所以如果不小心删除掉了，通常是很容易恢复的。
