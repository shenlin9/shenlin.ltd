# chapter 7.1 : 选择修订版本

title: 选择修订版本
categories:
  - Git
  - Book-ProGit
tags:
  - Git

---

## 简短 SHA-1

Git 十分智能，你只需要提供 SHA-1 的前几个字符就可以获得对应的那次提交，当然你提供的 SHA-1 字符数量不得少于 4 个，并且没有歧义

Git 可以为 SHA-1 值生成出简短且唯一的缩写。 如果你在 git log 后加上 --abbrev-commit 参数，输出结果里就会显示简短且唯一的值；默认使用七个字符，不过有时为了避免 SHA-1 的歧义，会增加字符数：

$ git log --abbrev-commit --pretty=oneline

通常 8 到 10 个字符就已经足够在一个项目中避免 SHA-1 的歧义。

比如 Linux 内核这个相当大的 Git 项目，目前有超过 45 万个提交，包含 360 万个对象，也只需要前 11 个字符就能保证唯一性。

SHA-1 摘要长度是 20 字节，也就是 160 位， 2^80 个随机哈希对象才有 50% 的概率出现一次冲突 （计算冲突机率的公式是 p = (n(n-1)/2) * (1/2^160)) ）。2^80 是 1.2 x 10^24 也就是一亿亿亿。 那是地球上沙粒总数的 1200 倍。

## 分支引用

查看分支对应的SHA-1
```
$ git rev-parse master
a20cf782c1a5c2a409c394305b5d24e872de9405
```

## 引用日志

每当 HEAD 指向的位置发生变化，Git 就会把变化信息存储到引用日志文件里，引用日志记录了最近几个月 HEAD 和分支引用所指向的变化历史

引用日志只存在于本地仓库，是一个记录用户在自己的仓库里做过什么的日志，任何人拷贝的仓库里的引用日志都是不相同的；

新克隆一个仓库的时候，引用日志是空的，因为你在仓库里还没有操作。

git show HEAD@{2.months.ago} 这条命令只有在你克隆了一个项目至少两个月时才会有用——如果你是五分钟前克隆的仓库，那么它将不会有结果返回。

查看引用日志
```
$ git reflog
```

使用 git log 的 -g 参数也可查看引用日志
```
$ git log -g --pretty=oneline --abbrev-commit
```
* -g 或 ----walk-reflogs

## ref@{Nth} 和 ref@{timestamp} 语法

使用`@{n}`来查看前 n 次的 HEAD 指向
```
$ git reflog HEAD@{5}

$ git log --oneline HEAD@{5}
```

查看昨天的 master 指向
```
$ git show master@{yesterday}
```

??? 这个遇到合并提交的分支时，怎么显示，还是只是针对 reflog 文件

## 祖先引用

祖先引用是另一种指明一个提交的方式。

假设提交历史如下

```
$ git log --pretty=format:'%h %s' --graph
* 734713b fixed refs handling, added gc auto, updated tests
*   d921970 Merge commit 'phedders/rdocs'
|\
| * 35cfb2b Some rdoc changes
* | 1c002dd added some blame and merge stuff
|/
* 1c36188 ignore *.gem
* 9b29157 add open3_detach to gemspec file list
```

### ref^[n] 和 ref~[n] 语法

如果在引用的尾部加上一个`^` 或 `~`， Git 会将其解析为该引用的第一个父提交。

```
$ git show HEAD^
commit d921970aadf03b3cf0e71becdaab3147ba71cdef

$ git show HEAD~
commit d921970aadf03b3cf0e71becdaab3147ba71cdef
```

在`~`后面加一个数字 n 表示向上溯第 n 个第一父提交，如`HEAD~2`表示第一父提交的第一父提交

在`^`后面加一个数字表示第二父提交，这个语法只适用于合并(merge)提交，因为合并提交才会有多个父提交。 第一父提交是你合并时所在分支，而第二父提交是你所合并的分支

可以组合使用 `~` 和 `^`，如 `HEAD~2^2` 表示当前引用的祖父提交的第二父提交

??? `^` 后面跟数字是不是只能2

```
$ git show d921970^
commit 1c002dd4b536e7479fe34593e72e6c6c1819e53b

$ git show d921970^2
commit 35cfb2b795a55793d7cc56a6cc2060b4bb732548

$ git show HEAD~3
commit 1c3618887afb5fbcbea25b7c013f4e2114448b8d

$ git show HEAD^^^
commit 1c3618887afb5fbcbea25b7c013f4e2114448b8d
```

## 提交区间


### 双点

查看在 dev 分支而不在 master 分支中的提交
```
$ git log master..dev
```

查看即将推送到服务器的提交，HEAD 可省略
```
$ git log origin/master..HEAD
$ git log origin/master..
```

查看要合并到本地分支的服务器提交，HEAD 可省略
```
$ git log HEAD..origin/master
$ git log HEAD..
```
双点语法很方便，但限制是只能指定两个分支

### 取反

引用前加 `^` 或 `--not` 表示提交不在的分支，下面 3 个命令等价
```
$ git log master..dev
$ git log ^master dev
$ git log dev --not master
```

这个语法比双点语法的优点在于可以指定两个以上的分支，

如查看被 dev, next 包含但不在 master 中的提交

```
$ git log dev next ^master
$ git log dev next --not master
```

### 三点

查看被 master 包含或被 dev 包含但没有被两个分支同时包含的提交

```
$ git log master...dev
$ git log --left-right master...dev
< commit1
> commit2
```
* 参数 --left-right 表示显示出提交到底在左边分支还是右边分支
* 左边分支前显示 &lt; 右边分之前显示 &gt;










