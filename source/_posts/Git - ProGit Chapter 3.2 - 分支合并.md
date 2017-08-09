title: chapter 3.2 - Git 分支合并
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-Branch

---

<!--more-->

### 合并分支

#### 快进合并

```
$ git checkout master
$ git merge hotfix
```

![](https://git-scm.com/book/en/v2/images/basic-branching-4.png)

* 当试图合并两个分支时，如果顺着一个分支走下去能够到达另一个分支，那么 Git 在合并两者的时候，只会简单的将指针向前推进（指针右移），因为这种情况下的合并操作没有需要解决的分歧——这就叫做 “快进（fast-forward）”。

![](https://git-scm.com/book/en/v2/images/basic-branching-5.png)

#### 三方合并

```
$ git checkout master
$ git merge iss53
```

![](https://git-scm.com/book/en/v2/images/basic-merging-1.png)

* 不能执行快进合并的时候，Git 会使用两个分支的末端所指的快照（C4 和 C5）以及这两个分支的工作祖先（C2），做一个简单的三方合并。

![](https://git-scm.com/book/en/v2/images/basic-merging-2.png)

* Git 将此次三方合并的结果做了一个新的快照并且自动创建一个新的提交指向它。 这个被称作一次合并提交，它的特别之处在于他有不止一个父提交。
* Git 会自行决定选取哪一个提交作为最优的共同祖先，并以此作为合并的基础。

#### 合并冲突

合并冲突时，git 会暂停合并，不会创建提交对象，但没有冲突的文件已合并，有冲突的文件则会在冲突文件中加入标准的冲突解决标记：
```
<<<<<<< HEAD
github pages test from you
=======
github pages test from me
>>>>>>> hotfix
```
表示 HEAD 指向的当前分支和 hotfix 分支有冲突，
=== 上面的是当前分支的内容，下面的是 hotfix 的内容，需要手动解决，即删除<<< === >>> 标记的行，然后冲突代码留下一个你想保留的版本。
            
#### 合并工具

git 中默认的合并工具可能随系统不同而不同，合并工具是用来解决冲突的

显示可用的合并工具
```
$ git mergetool --tool-help
```

设置合并工具
```
$ git config merge.tool winmerge
```

使用合并工具解决冲突
```
//使用系统设置的合并工具 
$ git mergetool

//使用指定的合并工具
$ git mergetool --tool=vimdiff
```

退出合并工具后，git 会询问刚才的合并是否成功，回答是则暂存文件
```
$ git mergetool
Merging:
index.html

Normal merge conflict for 'index.html':
  {local}: modified file
  {remote}: modified file
index.html seems unchanged.
Was the merge successful [y/n]? n
merge of index.html failed
Continue merging other unresolved paths [y/n]? n
```





