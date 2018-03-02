---
title: chapter 3.1 - Git 分支基础
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-Branch
---

git 的分支，其实本质上仅仅是指向提交对象的可变指针，每次提交后，分支都会向前移动指向最后的提交对象。

<!--more-->

## 分支简介

git 的分支实质上仅是包含所指对象校验和（长度为 40 的 SHA-1 值字符串）的文件，所以创建和销毁都异常高效。 创建一个新分支就相当于往一个文件中写入 41 个字节（40 个字符和 1 个换行符）

git 分支简单和高效，鼓励开发人员频繁地创建和使用分支。

git 分支--> commit 对象 --> tree 对象（内容快照）

commit 对象组成：

* 暂存区内容快照 tree 对象
* 作者的姓名和邮箱
* 提交时输入的信息
* 指向它的父对象的指针。
    * 首次提交产生的提交对象没有父对象
    * 普通提交操作产生的提交对象有一个父对象
    * 由多个分支合并产生的提交对象有多个父对象

git 默认分支名是 master，和其他分支一样，并没有特殊性，仅仅是因为`git init`命令默认创建的分支。

HEAD 特殊指针指向当前所在的本地分支

![分支及提交历史](https://git-scm.com/book/en/v2/images/branch-and-history.png)

## 分支典型模式

### 长期分支

使用多个长期分支的方法并非必要，但是这么做通常很有帮助，尤其是在一个非常庞大或者复杂的项目中工作时。

这么做的目的是使分支具有不同级别的稳定性；当它们具有一定程度的稳定性后，再把它们合并入具有更高级别稳定性的分支中。

通常把他们想象成流水线（work silos）可能更好理解一点，那些经过测试考验的提交会被遴选到更加稳定的流水线上去。

渐进稳定分支的流水线（“silo”）视图
![](https://git-scm.com/book/en/v2/images/lr-branches-2.png)

#### 1. master 分支
保留完全稳定的代码——有可能仅仅是已经发布或即将发布的代码。 

#### 2. develop 或者 next 分支
被用来做后续开发或者测试稳定性——这些分支不必保持绝对稳定，但是一旦达到稳定状态，它们就可以被合并入 master 分支了。

#### 3. proposed（建议）或 updates（建议更新）分支
可能因包含一些不成熟的内容而不能进入 next 或者 master 分支。 
 
### 特性分支

特性分支是一种短期分支，它被用来实现单一特性或其相关工作，对任何规模的项目都适用。 

特性分支使你的工作被分散到不同的流水线中，在不同的流水线中每个分支都仅与其目标特性相关，因此，在做代码审查之类的工作的时候就能更加容易地看出你做了哪些改动。

你可以把做出的改动在特性分支中保留几分钟、几天甚至几个月，等它们成熟之后再合并，而不用在乎它们建立的顺序或工作进度。

![](https://git-scm.com/book/en/v2/images/topic-branches-1.png)

## 分支操作

### 创建切换分支

```
//创建分支，未切换到新分支上
$ git branch newbranch

//切换到新分支
$ git checkout newbranch

//创建并切换到新分支
$ git checkout -b newbranch
```

切换分支时：
1. 把 HEAD 指针指向要切换到的分支
2. 把工作目录恢复成目标分支的快照内容，Git 会自动添加、删除、修改文件以确保此时你的工作目录和这个分支最后一次提交时的样子一模一样。如果 git 不能干净利落的完成任务，如有未提交的修改文件，git 将禁止切换

### 查看分支

#### 分支列表
```
$ git branch
  iss53
* master
  testing
```
\* 号表示 HEAD 指针指向的分支

#### 查看每个分支最后一次提交
```
$ git branch -v
  dev     a3b00c3 now the onlien one more new
  hotfix  912d37a conflict test
* master  7a23f52 [ahead 5, behind 1] conflict test
  monster d0135c0 index.html add
```

#### 查看哪些儿分支已经合并到当前分支
```
$ git branch --merged
  iss53
* master
```
这个列表里不带 * 号的分支通常可以`git branch -d` 删掉，因为都已经合并到当前分支，不会丢失数据

#### 查看哪些儿分支没有合并到当前分支
```
$ git branch --no-merged
  dev
  hotfix
  monster
```
* 这个列表里的分支不能被'git branch -d'删除，因为还未被合并到当前分支，可能丢失数据
* 要删除必须使用`-D`选项强制删除,即`git branch -D`

### 删除分支
```
$ git branch -d hotfix
```

### 合并分支

见下一章节 chapter3.2
