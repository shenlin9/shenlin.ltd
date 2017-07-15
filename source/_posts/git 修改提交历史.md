# git 修改提交历史

标签（空格分隔）： AAA Book-ProGit git

---

## 历史里的大文件问题

1. git clone 命令
git clone 会下载整个项目的历史，包括每个文件的每个版本，如果之前项目添加过一个大文件，即使以后大文件移除了，每次 clone 仍然要下载这个大文件，因为它在历史中存在，就会永远在那里。

2. 迁移版本库
迁移 Subversion 或 Perforce 仓库到 Git 的时候，历史里的大文件是一个严重的问题。因为这些版本控制系统并不下载所有的历史文件，所以这种文件所带来的问题比较少。 如果你从其他的版本控制系统迁移到 Git 时发现仓库比预期的大得多，那么你就需要找到并移除这些大文件。

## 警告

**警告：这个操作对提交历史的修改是破坏性的。**

它会从大文件引用最早的树对象开始重写每一次提交。

如果你在导入仓库后，在任何人开始基于这些提交工作前执行这个操作，那么将不会有任何问题 - 否则，你必须通知所有的贡献者他们需要将他们的成果变基到你的新提交上。

## 步骤演示

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
