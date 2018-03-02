---
title: chapter 3.4 - Git 变基
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-Rebase
---

git 中整合分支主要两个方法：合并 和 变基。

<!--more-->

## 合并和变基

举例说明：开发任务分叉到两个不同分支： experiment 和 master

![](https://git-scm.com/book/en/v2/images/basic-rebase-1.png)

## 使用 merge 命令合并

```
$ git checkout master
$ git merge experiment
```

会把两个分支的最新快照 C3 和 C4 以及两者最近的共同祖先 C2 进行三方整合，合并的结果是生成一个新快照 C5 并提交。

![](https://git-scm.com/book/en/v2/images/basic-rebase-2.png)

## 使用 rebase 命令合并

### 变基合并过程

#### 1. 先变基

```
$ git checkout experiment
$ git rebase master

//上面的两条命令也可以简化为：
$ git rebase master experiment
```

将提取在 C4 中引入的补丁和修改，然后在 C3 的基础上应用一次。 在 Git 中，这种操作就叫做 变基。 
rebase 命令可以将提交到某一分支上的所有修改都移至另一分支上，就好像“重新播放”一样。

具体原理：
1. 找到当前分支最新快照 C4、目标基底分支最新快照 C3 的最近共同祖先 C2
2. 对比当前分支 C4 相对于祖先 C2 的历次提交，提取相应的修改并存为临时文件
3. 把当前分支指向目标基底 C3
4. 将之前另存为临时文件的修改依序应用。


![](https://git-scm.com/book/en/v2/images/basic-rebase-3.png)

#### 2. 再快进合并

回到 master 分支，执行一次快进合并

```
$ git checkout master
$ git merge experiment
```
![](https://git-scm.com/book/en/v2/images/basic-rebase-4.png)


### 把“重放”应用到第三方分支

在对两个分支进行变基时，所生成的“重放”并不一定要在目标分支上应用，也可以把“重放”应用到另外的分支，使用`--onto`选项指定新基底分支： 

`git rebase --onto NewBase basebranch topicbranch`

举例说明：

现有分支如下，master、server、client，要把 client 分支的 C8, C9 提交更新到 master 分支，但不包含 server 的 C3 提交

![](https://git-scm.com/book/en/v2/images/interesting-rebase-1.png)

则可以执行命令：
```
$ git rebase --onto master server client
```

![](https://git-scm.com/book/en/v2/images/interesting-rebase-2.png)

再执行一次快进合并即可
```
$ git checkout master
$ git merge client
```

![](https://git-scm.com/book/en/v2/images/interesting-rebase-3.png)

把 server 分支也变基到 master
```
$ git rebase master server
```
![](https://git-scm.com/book/en/v2/images/interesting-rebase-4.png)

再次执行快进合并
```
$ git checkout master
$ git merge server
```

删除分支
```
$ git branch -d client
$ git branch -d server
```

![](https://git-scm.com/book/en/v2/images/interesting-rebase-5.png)

## 合并和变基区别

由上面的例子可以看出：

变基是将一系列提交按照原有次序依次应用到另一分支上，而合并是把最终结果合在一起。

合并和变基这两种方法的整合结果没有区别，但提交历史不同，**变基使得提交历史变得更加简洁，查看一个经过变基的分支的历史记录时会发现，尽管实际的开发工作是并行的，但它们看上去就像是串行的一样，提交历史是一条直线没有分叉**。

## 使用变基的目的

使用变基的目的是为了确保在向远程分支推送时能保持提交历史的整洁——例如向某个其他人维护的项目贡献代码时，你首先在自己的分支里进行开发，当开发完成时你需要先将你的代码变基到 origin/master 上，然后再向主项目提交修改。这样的话，该项目的维护者就不再需要进行整合工作，只需要快进合并便可。

## 使用变基要注意的问题

![](https://git-scm.com/book/en/v2/images/perils-of-rebasing-1.png)

上图为最初的服务器和本地分支情况

![](https://git-scm.com/book/en/v2/images/perils-of-rebasing-4.png)

PersonA 把自己的更新 C5,C4 及一个合并提交 C6 提交到了远程库，然后 PersonB 从远程库 fetch 下来并 merge 到自己的本地分支，然后在此基础上开发，后来 PersonA 又把合并操作 C6 回滚，丢弃了原来的提交 C4,C6 ，改用变基的方式 C4' 重新提交了相同的代码，并使用`git push --force`覆盖了服务器上的提交历史，再次提交到了远程库，这时 PeronB 已经在基于 C5、C4、C6 的提交上做了本地开发，则 PersonB 再次拉取远程库时，就会出现问题：

1、会再次拉取 C4' 合并，但是 C4' 和 C4 的提交内容是相同的，只不过一个通过合并提交，一个通过变基提交的，同样的代码被合并了两次，而且两个提交对象的日期、作者、日志都是一样的

2、C4 已经被 PersonA 抛弃并被 C4' 替代，PersonB 在 push 到服务器时，会把抛弃的 C4 再次找回来

Question:
所以问题的根本就是代码被提交了两次吧：通过合并一次，通过变基一次

**不要对在你的仓库外有副本的分支执行变基。**

## 使用变基解决变基

Git 除了对整个提交计算 SHA-1 校验和以外，也对本次提交所引入的修改计算了校验和—— 即 “patch-id”。

如果你拉取被覆盖过的更新并将你手头的工作基于此进行变基的话，一般情况下 Git 都能成功分辨出哪些是你的修改，并把它们应用到新分支上。

如上例所示，如果团队中有人推送了经过变基的提交，并丢弃了你的本地开发所基于的一些提交，解决方法是：不执行合并，而是执行 `git rebase teamone/master`, Git 将会：

* 检查哪些提交是我们的分支上独有的（C2，C3，C4，C6，C7）
* 检查其中哪些提交不是合并操作的结果（C2，C3，C4）
* 检查哪些提交在对方覆盖更新时并没有被纳入目标分支（只有 C2 和 C3，因为 C4 其实就是 C4'）
* 把查到的这些提交应用在 teamone/master 上面

![](https://git-scm.com/book/en/v2/images/perils-of-rebasing-5.png)

下面三种命令执行方式效果相同：
```
//更改pull.rebase的默认配置，使得git pull时默认使用选项--rebase
$ git conifg --global pull.rebase true
$ git pull

//自动获取并变基
$ git pull --rebase

//手动获取并变基
$ git fetch
$ git rebase teamone/master
```
要想上述方案有效，还需要对方在变基时确保 C4' 和 C4 是几乎一样的。

## 变基和合并到底哪种方式更好？

在回答这个问题之前，让我们先讨论一下提交历史到底意味着什么。

有一种观点认为，仓库的提交历史即是 **记录实际发生过什么**。
它是针对历史的文档，本身就有价值，不能乱改。
从这个角度看来，改变提交历史是一种亵渎，你使用_谎言_掩盖了实际发生过的事情。 
如果由合并产生的提交历史是一团糟怎么办？ 既然事实就是如此，那么这些痕迹就应该被保留下来，让后人能够查阅。

另一种观点则正好相反，他们认为提交历史是 **项目过程中发生的事**。 没人会出版一本书的第一版草稿，软件维护手册也是需要反复修订才能方便使用。 
持这一观点的人会使用 rebase 及 filter-branch 等工具来编写故事，怎么方便后来的读者就怎么写。

现在，让我们回到之前的问题上来，到底合并还是变基好？

希望你能明白，这并没有一个简单的答案。

Git 是一个非常强大的工具，它允许你对提交历史做许多事情，但每个团队、每个项目对此的需求并不相同。

既然你已经分别学习了两者的用法，相信你能够根据实际情况作出明智的选择。

**总的原则是，只对尚未推送或分享给别人的本地修改执行变基操作清理历史，从不对已推送至别处的提交执行变基操作，这样，你才能享受到两种方式带来的便利。**
