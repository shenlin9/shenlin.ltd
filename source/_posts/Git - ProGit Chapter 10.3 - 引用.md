---
title: chapter 10.3 - Git 引用
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git 引用
---

使用一个文件来保存 commit 的 SHA 值，给文件取一个简单的名字，然后用这个简单的文

件名字指针来代替复杂的 SHA 值，这样的文件在 git 里就被称为引用 references，缩写

refs。

<!--more-->

这也是 git 分支的本质，即把一个引用指向一个 commit 对象。

![git 对象关系图](https://www.git-scm.com/book/en/v2/images/data-model-4.png)

git 里的所有引用都保存在 .git/refs 目录里：

```
$ find .git/refs
.git/refs
.git/refs/heads
.git/refs/heads/dev
.git/refs/heads/dev-beta
.git/refs/heads/master
.git/refs/heads/newbranch
.git/refs/heads/origin
.git/refs/remotes
.git/refs/remotes/origin
.git/refs/remotes/origin/dev
.git/refs/remotes/origin/dev-beta
.git/refs/remotes/origin/HEAD
.git/refs/remotes/origin/master
.git/refs/tags
.git/refs/tags/0.8
.git/refs/tags/0.9
.git/refs/tags/1.0
```

有个问题，就是执行`git log`，`git status`，`git commit`等命令时，我们针对的都是哪个分支或哪个`commit`对象？

这个问题由 HEAD 引用解决。

## HEAD 引用

在 Git 中，HEAD 是一个指针，指向当前所在的本地分支（译注：将 HEAD 想象为当前分支的别名）。

HEAD 引用是一个符号引用，指向当前操作环境所在的分支。

符号引用意味着不像普通引用一样包含一个 SHA 值，而是指向其他引用的指针。

```
$ cat .git/HEAD
ref: refs/heads/dev
```

切换分支时，HEAD 引用也会更新指向：
```
$ git checkout master
$ cat .git/HEAD
ref: refs/heads/master
```

## 标签引用

标签对象像分支引用一样，也是指向一个 commit 对象，但分支对象可更新引用指向其他 commit 对象，而标签对象是永远不移动的，始终指向同一个 commit 对象，只不过给 commit 对象添加了一个更友好的名字而已。

(其实标签对象的指向不限定在commit对象，可指向任何git对象类型)

### 两种标签

标签分为轻量标签和附注标签。

轻量标签就是一个引用，直接指向 commit 对象。

附注标签指向的是一个标签对象，再根据标签对象里的 object 属性指向 commit 对象。

附注标签保存的内容：

    object  指向被标签对象的指针
    type    被标签对象类型
    tag     标签
    tagger  标签创建者信息和时间戳
    注释信息

通常建议创建附注标签，这样可以保存以上所有信息；
轻量标签建议作为一个临时的标签，或者因为某些原因不想要保存上述信息时使用

附注标签还可以使用 GNU Privacy Guard （GPG）签名与验证。

* 创建标签

```
//创建轻量标签
$ git tag 1.0   

//创建附注标签
$ git tag -a 1.1 -m "software go to 1.1"

//创建带签名的附注标签
git tag -s 1.2 -m "software release 1.2"

//验证附注标签的签名
git tag -v 1.2
```

轻量标签不要加 -m 注释等参数，即使加上也不会被记录

`-a` 选项表示创建附注标签

* 显示标签

```
//轻量标签只显示 commit 对象信息
$ git show 1.0
commit 4a1c5c6738d89357afdc145664498e9647cfbdd3 (tag: 1.0)
Author: bitbite <bitbite@foxmail.com>
Date:   Sun Jun 11 11:35:21 2017 +0800

    modified branch dev again

//附注标签还会显示 tag 对象信息
$ git show 1.1
tag 1.1
Tagger: bitbite <bitbite@foxmail.com>
Date:   Wed Jun 14 13:41:23 2017 +0800

1.1 released

commit a11aed78491dff564a79a3a49b6e24d33dfc487d (tag: 1.1, origin/master, origin/HEAD, master)
Merge: 59d4a43 4a1c5c6
Author: bitbite <bitbite@foxmail.com>
Date:   Sun Jun 11 11:40:35 2017 +0800

    merge user master version

```

### 验证标签的指向

```
//查看轻量标签的 SHA 值
$ cat .git/refs/tags/1.0
4a1c5c6738d89357afdc145664498e9647cfbdd3

//轻量标签直接指向 commit 对象
$ git cat-file -t 4a1c5c6738d89357afdc145664498e9647cfbdd3
commit

//查看附注标签 SHA 值
$ cat .git/refs/tags/1.1
0debf48d88325174045245575c3fea76a8d166cc

//附注标签指向的是一个标签对象
$ git cat-file -t 0debf48d88325174045245575c3fea76a8d166cc
tag

//再用 object 属性指向 commit 对象
$ git cat-file -p 0debf48d88325174045245575c3fea76a8d166cc
object fd61fc5d10a311ad261c9b1a2ff22b09faf0a776
type commit
tag 1.1
tagger bitbite <bitbite@foxmail.com> 1497337859 +0800

software go to 1.1

//
$ git cat-file -t fd61fc5d10a311ad261c9b1a2ff22b09faf0a776
commit
```

## 远程引用

当添加一个远程版本库并执行`push`操作，即创建一个远程引用，保存在.git/refs/remotes目录下

`.git/refs/remotes/origin/*` 存储的是和远程分支对应的本地分支最后一次 commit 对象的引用

```
$ git checkout -b monster
Switched to a new branch 'monster'

ssl@sslnotebook MINGW32 /d/gitgo (monster)
$ find .git/refs/remotes
.git/refs/remotes/origin/master

$ git remote add origin git@github.com:bitbiteme/gitgo

//推送
$ git push --set-upstream origin monster
 * [new branch]      monster -> monster
Branch monster set up to track remote branch monster from origin.

$ find .git/refs/remotes
.git/refs/remotes/origin/master
.git/refs/remotes/origin/monster

//指向的都是最后一次的commit对象
$ cat .git/refs/remotes/origin/monster
fd61fc5d10a311ad261c9b1a2ff22b09faf0a776

$ cat .git/refs/heads/monster
fd61fc5d10a311ad261c9b1a2ff22b09faf0a776

$ git cat-file -t fd61fc5d10a311ad261c9b1a2ff22b09faf0a776
commit
```

远程引用和分支引用之间最主要的区别在于：远程引用是只读的。 

虽然可以 git checkout 到某个远程引用，但是 Git 并不会将 HEAD 引用指向该远程引用。因此，你永远不能通过 commit 命令来更新远程引用。

Git 将这些远程引用作为记录远程服务器上各分支最后已知位置状态的书签来管理。


## 引用相关命令

### 非符号引用

更新分支引用指向，可直接编辑引用文件，但**不建议**
```
$ echo "1a410efbd13591db07496601ebc7a059dd55cfe9" > .git/refs/heads/master
```

**建议使用更新命令`update-ref`**
```
$ git update-ref refs/heads/master 1a410efbd13591db07496601ebc7a059dd55cfe9
```

`update-ref`就是分支的本质，如新建分支 newbranch
```
$ git update-ref refs/heads/newbranch 1a410efbd13591db07496601ebc7a059dd55cfe9
```

### 符号引用

以 HEAD 符号引用为例

**直接操作文件，不建议使用**

查看HEAD指向
```
$ cat .git/HEAD
ref: refs/heads/dev
```

修改HEAD指向
```
ssl@sslnotebook MINGW32 /d/gitgo (master)
$ echo ref: refs/heads/dev > .git/HEAD

ssl@sslnotebook MINGW32 /d/gitgo (dev)
```

**建议使用更安全的操作命令`symbolic-ref`**

查看HEAD指向
```
$ git symbolic-ref HEAD
refs/heads/dev
```

修改HEAD指向
```
ssl@sslnotebook MINGW32 /d/gitgo (dev)      //<--原来分支
$ git symbolic-ref HEAD refs/heads/master

ssl@sslnotebook MINGW32 /d/gitgo (master)  //<--切换分支了
```

HEAD 不能指向`refs/`外边
```
$ git symbolic-ref HEAD refsFormatWrong
fatal: Refusing to point HEAD outside of refs/

$ git symbolic-ref HEAD refs/NoThisBranch

$ git symbolic-ref HEAD
refs/NoThisBranch
```
