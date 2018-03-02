---
title: chapter 7.3 - Git 储藏和清理
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-Stash
---

在一个项目上开发一段时间后，一切进入混乱状态，这时因为 debug 等原因需要进入另一个分支，

但又不想将当前混乱的工作做一次提交，则可以使用 `git stash`， `git stash` 会将未完成的修改保存到一个栈上。

<!--more-->

.git/refs/stash

## 储藏列表

```
$ git stash list
stash@{0}: WIP on feature: 9d78617 test22
stash@{1}: WIP on feature: a7d787c test
stash@{2}: WIP on feature: a1eb720 now give you the reason
```

## 创建储藏

创建基本储藏
```
$ git stash
$ git stash save
```

互动式创建储藏
```
$ git stash --patch
```

创建储藏时把未被跟踪的文件也包含了
```
$ git stash -u
```

创建储藏后清理目录为干净状态
```
$ git stash --all
```

创建储藏时忽略已添加到 Index 中的文件
```
$ git stash --keep-index
```

把当前的脏目录创建为分支
```
$ git stash branch testchange
```

## 应用储藏

创建储藏和应用储藏的分支不是必须为同一个分支

应用最后的储藏
```
$ git stash apply
```

应用指定的储藏
```
$ git stash apply stash@{2}
```

应用储藏时恢复 Index
```
$ git stash apply --index
```
* 默认是不还原文件的暂存状态的

应用最后的储藏，完成后删除此储藏
```
$ git stash pop
```

## 删除储藏

删除最后的储藏
```
$ git stash drop
```

删除指定的储藏
```
$ git stash drop stash@{2}
```

## 清理工作目录

默认 clean 命令只移除未被跟踪且未被忽略的文件，被移除的文件无法找回，使用 `git stash --all` 更安全

```
$ git clean
```
* -d 清理目录
* -n 预演，不实际执行
* -f 强制
* -x 移除被忽略的文件
* -i interactive 互动式清理






