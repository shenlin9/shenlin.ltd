title: chapter 7.2 - Git 交互式暂存
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-Stage

---

使用交互式命令可以把文件的特定部分组合成提交。

若修改了一组文件，希望这些儿改动提交到若干个提交，而不是混杂的一个提交，这样可以确保提交是逻辑上独立的变更集合，同时使得提交记录更容易审核。

<!--more-->

进入交互提交模式
```
$ git add -i

*** Commands ***
  1: status       2: update       3: revert       4: add untracked
  5: patch        6: diff         7: quit         8: help
What now> q
Bye.
```
* -i 或 -interactive

按提示进行操作即可，

按 `u` 或 2 表示要更新暂存区，会给出文件列表让你选择，如果提示符后没有输入编号，直接回车则表示选择完毕将暂存之前选择的文件

按 `p` 或 5 表示要文件的特定部分，如可能修改了两处，但只想暂存一处


命令的补丁模式也可实现同样效果
```
git add -p
git add --patch

//部分重置文件
$ git reset --patch

//部分检出文件，在文件修改了以后
$ git checkout --patch

//部分暂存文件
$ git stash --patch
```

测试失败，总提示“No changes.”



