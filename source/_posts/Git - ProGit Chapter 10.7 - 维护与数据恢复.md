---
title: chapter 10.7 - Git 维护与数据恢复
categories: Git
tags: Git
---

Git 维护与数据恢复

<!--more-->

## 维护

## 数据恢复（找回丢失提交）

目前版本库提交记录如下：

```
$ git log --pretty=oneline
ab1afef80fac8e34258ff41fc1b867c702daa24b modified repo a bit
484a59275031909e19aadb7c92262719cfcdf19a added repo.rb
1a410efbd13591db07496601ebc7a059dd55cfe9 third commit
cac0cab538b970a37ea1e769cbbde608743bc96d second commit
fdf4fc3344e67ab068f836878b6c4951e3b15f3d first commit
```
如果强制删除了正在工作的分支，但是最后却发现你还需要这个分支；
亦或者硬重置了一个分支，放弃了想要的提交；
如何找回丢失的提交？

### 1. 有引用日志的情况

模拟丢失两个提交，将 master 分支硬重置到第三次提交：
```
$ git reset --hard 1a410efbd13591db07496601ebc7a059dd55cfe9
HEAD is now at 1a410ef third commit

$ git log --pretty=oneline
1a410efbd13591db07496601ebc7a059dd55cfe9 third commit
cac0cab538b970a37ea1e769cbbde608743bc96d second commit
fdf4fc3344e67ab068f836878b6c4951e3b15f3d first commit
```
这时没有分支指向那两个丢失的提交，办法是找出丢失的提交SHA，然后创建分支指向它们即可。

每一次提交或改变分支时，HEAD值的改变都会被git记录下来，就是引用日志(reflog).

查看reflog日志：
```
$ git reflog
1a410ef HEAD@{0}: reset: moving to 1a410ef
ab1afef HEAD@{1}: commit: modified repo.rb a bit
484a592 HEAD@{2}: commit: added repo.rb
```

或以标准日志的格式输出引用日志
```
$ git log -g
commit 1a410efbd13591db07496601ebc7a059dd55cfe9
Reflog: HEAD@{0} (Scott Chacon <schacon@gmail.com>)
Reflog message: updating HEAD
Author: Scott Chacon <schacon@gmail.com>
Date:   Fri May 22 18:22:37 2009 -0700

		third commit

commit ab1afef80fac8e34258ff41fc1b867c702daa24b
Reflog: HEAD@{1} (Scott Chacon <schacon@gmail.com>)
Reflog message: updating HEAD
Author: Scott Chacon <schacon@gmail.com>
Date:   Fri May 22 18:15:24 2009 -0700

       modified repo.rb a bit
```

找到了丢失的提交，创建一个新的分支指向这个提交来恢复它
```
$ git branch recover-branch ab1afef

$ git log --pretty=oneline recover-branch
ab1afef80fac8e34258ff41fc1b867c702daa24b modified repo a bit
484a59275031909e19aadb7c92262719cfcdf19a added repo.rb
1a410efbd13591db07496601ebc7a059dd55cfe9 third commit
cac0cab538b970a37ea1e769cbbde608743bc96d second commit
fdf4fc3344e67ab068f836878b6c4951e3b15f3d first commit
```

### 2. 没有引用日志的情况

如果分支和日志都被删除
```
$ git branch -D recover-branch
$ rm -Rf .git/logs/
```

使用命令显示没有被其他对象指向的对象
```
$ git fsck --full
Checking object directories: 100% (256/256), done.
Checking objects: 100% (18/18), done.
dangling blob d670460b4b4aece5915caf5c68d12f560a9fe3e4
dangling commit ab1afef80fac8e34258ff41fc1b867c702daa24b
dangling tree aea790b9a58f6cf6f2804eeac9f0abbe9631e4c9
dangling blob 7108f7ecb345ee9d0084193f147cdad4d2998293
```
dangling commit 后面即是丢失的提交对象,找到后建立分支指向即可

## 移除对象（修改提交历史）

### 历史里的大文件问题

1. git clone 命令
git clone 会下载整个项目的历史，包括每个文件的每个版本，如果之前项目添加过一个大文件，即使以后大文件移除了，每次 clone 仍然要下载这个大文件，因为它在历史中存在，就会永远在那里。

2. 迁移版本库
迁移 Subversion 或 Perforce 仓库到 Git 的时候，历史里的大文件是一个严重的问题。因为这些版本控制系统并不下载所有的历史文件，所以这种文件所带来的问题比较少。 如果你从其他的版本控制系统迁移到 Git 时发现仓库比预期的大得多，那么你就需要找到并移除这些大文件。

### 警告

**警告：这个操作对提交历史的修改是破坏性的。**

它会从大文件引用最早的树对象开始重写每一次提交。

如果你在导入仓库后，在任何人开始基于这些提交工作前执行这个操作，那么将不会有任何问题 - 否则，你必须通知所有的贡献者他们需要将他们的成果变基到你的新提交上。

### 步骤演示

#### 1. 查看空间占用
```
$ git count-objects -v
count: 7
size: 32
in-pack: 17
packs: 1
size-pack: 4868
prune-packable: 0
garbage: 0
size-garbage: 0
```
size        是松散文件的大小
size-pack   包文件大小
都以 KB 为单位

#### 2.找出大文件

执行 git gc 命令，所有的对象将被放入一个包文件中
```
$ git gc --auto
```

执行 git verify-pack 命令，对输出内容的第三列（文件大小）进行排序找出大文件
```
$ git verify-pack -v .git/objects/pack/pack-29…69.idx \
  | sort -k 3 -n \
  | tail -3
dadf7258d699da2c8d89b09ef6670edb7d5f91b4 commit 229 159 12
033b4468fa6b2a9547a70d88d1bbe8bf3f9ed0d5 blob   22044 5792 4977696
82c99a3e86bb1267b236a4b6eff7868d97489af1 blob   4975916 4976258 1438
```

#### 3. 找出大文件路径

根据 SHA 找出具体是哪个文件
```
$ git rev-list --objects --all | grep 82c99a3
82c99a3e86bb1267b236a4b6eff7868d97489af1 git.tgz
```

#### 4. 查看大文件最早的提交 SHA

查看哪些提交对这个文件产生改动：
```
$ git log --oneline --branches -- git.tgz
dadf725 oops - removed large tarball
7b30847 add git tarball
```
是 7b30847

#### 5. 重写提交从历史中移除

重写 7b30847 之后的所有提交，从 Git 历史中完全移除这个文件
```
$ git filter-branch --index-filter \
  'git rm --ignore-unmatch --cached git.tgz' -- 7b30847^..

Rewrite 7b30847d080183a1ab7d18fb202473b3096e9f34 (1/2)rm 'git.tgz'
Rewrite dadf7258d699da2c8d89b09ef6670edb7d5f91b4 (2/2)
Ref 'refs/heads/master' was rewritten
```
    filter-branch
    --index-filter  修改在暂存区或索引中的文件。
    --tree-filter   修改在硬盘上检出的文件
    -- 7b30847^..   重写自 7b30847 提交以来的历史，否则命令会从最旧的提交开始，会花费许多不必要的时间

    git rm
    必须使用 git rm --cached 命令来从索引中移除文件，
    而不是通过类似 rm file 的命令从磁盘中移除文件，
    从索引中移除速度会非常快，因为在运行过滤器之前，没必要检出每个修订版本到磁盘中。
    
    ??（慢）??也可以通过 --tree-filter 选项来完成同样的任务。
    
    --ignore-unmatch 选项告诉命令：如果尝试删除的模式不存在时，不提示错误。

#### 6. 移除包含指向旧提交的指针文件

经过上面几个步骤，git 提交历史中将不再包含对那个文件的引用。
但在重新打包前需要移除任何包含指向旧提交的指针文件：
1. 引用日志
2. .git/refs/original 通过 filter-branch 选项添加的新引用中还存有对这个文件的引用

```
$ rm -Rf .git/refs/original
$ rm -Rf .git/logs/
$ git gc
Counting objects: 15, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (15/15), done.
Total 15 (delta 1), reused 12 (delta 0)
```

#### 7. 完全移除大文件对象

大文件已不在包文件里，
也不会在推送或接下来的克隆中出现，这是最重要的。

```
$ git count-objects -v
count: 11
size: 4904
in-pack: 15
packs: 1
size-pack: 8
prune-packable: 0
garbage: 0
size-garbage: 0
```

但从 size 的值看出，大文件还在松散对象中，并没有消失；
如果真的想要删除它，可以通过有 --expire 选项的 git prune 命令来完全地移除那个对象：

```
$ git prune --expire now
$ git count-objects -v
count: 0
size: 0
in-pack: 15
packs: 1
size-pack: 8
prune-packable: 0
garbage: 0
size-garbage: 0
```

