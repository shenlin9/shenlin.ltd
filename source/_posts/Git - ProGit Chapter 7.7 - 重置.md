---
title: chapter 7.7 - Git 重置
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-Reset
  - Git-Checkout
---

reset 和 checkout 都是在操作 Git 的三棵树。

<!--more-->

## 三棵树

Git 里的树不是指特定的数据结构，而是指文件的集合。

Git 里包括了三棵树：HEAD, Index, Working Dir。

HEAD : 上次提交的快照

Index : 预期的下次的提交快照，索引也称为暂存区，技术上并不是树结构，而是使用扁平的清单实现的

Working Directory : 工作目录

## 重置

### 重置分支

初始状态：

![](https://www.git-scm.com/book/en/v2/images/reset-start.png)

根据选项不同，reset 的执行步骤也不同，最多 3 步，每一步对应一棵树：

`--soft` 把 HEAD 和 HEAD 指向的分支一起移动

![](https://www.git-scm.com/book/en/v2/images/reset-soft.png)

`--mixed` 除了上面的一步操作，再使用HEAD的版本更新索引，是 reset 的默认选项

![](https://www.git-scm.com/book/en/v2/images/reset-mixed.png)

`--hard` 除了上面的两步操作，再使用索引的版本更新工作目录，会强制覆盖工作目录文件比较危险

![](https://www.git-scm.com/book/en/v2/images/reset-hard.png)

### 重置文件

reset 后若是跟着路径则只执行第二步 : 使用 HEAD 的版本更新索引，并且把作用范围限定为指定的路径。

第一步因为一个 HEAD 指针无法指向多个文件会跳过不执行，soft 选项无效， 第三步因为路径模式不支持 hard 选项也不执行，所以只执行第二步。

![](https://www.git-scm.com/book/en/v2/images/reset-path1.png)

这时 `git reset file.txt` 相当于 `git add file.txt` 的相反命令

![](https://www.git-scm.com/book/en/v2/images/reset-path2.png)

`git reset file.txt` 是完整形式 `git reset --mixed HEAD file.txt` 的简写，所以也可以从其他分支或提交来提取文件：

![](https://www.git-scm.com/book/en/v2/images/reset-path3.png)

### 利用重置来压缩提交

压缩提交：如共有 n 个提交历史，但只想保留头尾两个历史，因为中间的提交历史都是阶段性的不成熟的，当然内容是保存完整的，仅仅是历史被压缩了。

如下图 3 个提交历史，每次都修改了 file-a.txt，中间的第二次提交对于 file-a.txt 来说是一个未完成的工作，想要压缩它：

https://www.git-scm.com/book/en/v2/images/reset-squash-r1.png

先移动 HEAD 分支到第一个提交 : `git reset --soft HEAD~2`

https://www.git-scm.com/book/en/v2/images/reset-squash-r2.png

这时 HEAD 和 Index 有差异了，HEAD 指向了第一次提交，Index 的内容仍然是第三次提交，再次提交: `git commit` 则相当于从第一次提交直接到了第三次的提交

https://www.git-scm.com/book/en/v2/images/reset-squash-r3.png

再查看历史就没有了第二次的提交

## 重置和检出

### 不带路径

#### `checkout` 和 `reset --hard` 

两者都会更新三棵树，但 `checkout` 对工作目录文件是安全的，检出前会检查不会强制覆盖文件

`reset --hard` 则不检查直接强制覆盖

#### 更新 HEAD 的方式

reset 时，会把 HEAD 本身和 HEAD 指向的分支一起移动，而 checkout 时则只移动 HEAD 本身的指向

![](https://www.git-scm.com/book/en/v2/images/reset-checkout.png)

### 带路径

`git checkout file-a.txt` 会从 HEAD 检出文件更新 Index 并强制覆盖工作目录，对工作目录是不安全的

`git reset file-a.txt` 没有 hard 选项，只更新 Index，不覆盖工作目录，对工作目录是安全的

### 总结


|| 	HEAD| 	Index| 	Workdir| 	WD Safe?|
|---|---||||
|Commit Level|||||
|reset --soft [commit]|REF|NO|NO|YES|
|reset [commit]|REF|YES|NO|YES|
|reset --hard [commit]|REF|YES|YES|NO|
|checkout [commit]|HEAD|YES|YES|YES|
|File Level|||||
|reset [commit] (file)|NO|YES|NO|YES|
|checkout [commit] (file)|NO|YES|YES|NO|

* “HEAD” 一列中的 “REF” 表示该命令移动了 HEAD 指向的分支引用，而 "HEAD" 则表示只移动了 HEAD 自身，NO 表示都没有移动。
* 特别注意 "WD Safe?" 一列 - 如果它标记为 NO，那么运行该命令之前需考虑一下。
