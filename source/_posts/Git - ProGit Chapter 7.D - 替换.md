---
title: chapter 7.D - Git 替换
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-Replace
---

Git 对象是不可变的，但 `replace` 命令可以指定一个对象 A 表面上替换另一个对象 B，

效果就是遇到对象 B 时，就去获取对象 A 。

<!--more-->

## 应用举例

一个大型的版本库，要拆分成两个版本库，一个短的版本库记录最新的提交给新的开发者使用，一个更大更长久的版本库记录老的提交给喜欢数据挖掘的人使用。

如下面的版本库，要将其拆分成两条历史。 第一个到第四个提交的作为第一个历史版本。 第四、第五个提交的作为最近的第二个历史版本

```
$ git log --oneline
ef989d8 fifth commit
c6e1e95 fourth commit
9c68fdc third commit
945704c second commit
c1822cf first commit
```

![replace1](https://www.git-scm.com/book/en/v2/images/replace1.png)

## 实现思路

通过用新仓库中最老的提交 replace 老仓库中最新的提交来连接历史，这种方式可以把一条历史移植到其他历史上。

## 实现过程

### 创建长历史

将一个历史中的分支推送到一个新的远程仓库的 master 分支
```
$ git branch history c6e1e95

$ git log --oneline --decorate
ef989d8 (HEAD, master) fifth commit
c6e1e95 (history) fourth commit         -->创建的新分支
9c68fdc third commit
945704c second commit
c1822cf first commit

$ git remote add project-history https://github.com/schacon/project-history

$ git push project-history history:master
```

![replace2](https://www.git-scm.com/book/en/v2/images/replace2.png)


### 创建短历史

#### 创建基础提交

```
$ echo 'get history from blah blah blah' | git commit-tree 9c68fdc^{tree}
622e88e9cbfbacfb75b5279245b9fb38dfea10cf
```

![replace3](https://www.git-scm.com/book/en/v2/images/replace3.png)

#### 将剩余的历史变基到基础提交之上

```
$ git rebase --onto 622e88 9c68fdc
First, rewinding head to replay your work on top of it...
Applying: fourth commit
Applying: fifth commit
```

![replace4](https://www.git-scm.com/book/en/v2/images/replace4.png)

#### asdf

#### 替换提交

```
$ git replace 81a708d c6e1e95

$ git log --oneline master
e146b5f fifth commit
81a708d fourth commit
9c68fdc third commit
945704c second commit
c1822cf first commit
```
* 即使是使用了 c6e1e95 来替换 81a708d，它的 SHA-1 仍显示为 81a708d
* 使用 cat-file 查看也是一样的结果
    ```
    $ git cat-file -p 81a708d
    tree 7bc544cf438903b65ca9104a1e30345eee6c083d
    parent 9c68fdceee073230f19ebb8b5e7fc71b479c0252
    author Scott Chacon <schacon@gmail.com> 1268712581 -0700
    committer Scott Chacon <schacon@gmail.com> 1268712581 -0700

    fourth commit
    ```
* 81a708d 真正的父提交是 622e882 占位提交，而非呈现的 9c68fdce 提交

![replace5](https://www.git-scm.com/book/en/v2/images/replace5.png)


```
$ git for-each-ref
e146b5f14e79d4935160c0e83fb9ebe526b8da0d commit	refs/heads/master
c6e1e95051d41771a649f3145423f8809d1a74d4 commit	refs/remotes/history/master
e146b5f14e79d4935160c0e83fb9ebe526b8da0d commit	refs/remotes/origin/HEAD
e146b5f14e79d4935160c0e83fb9ebe526b8da0d commit	refs/remotes/origin/master
c6e1e95051d41771a649f3145423f8809d1a74d4 commit	refs/replace/81a708dd0e167a3f691541c7a6463343bc457040
```
