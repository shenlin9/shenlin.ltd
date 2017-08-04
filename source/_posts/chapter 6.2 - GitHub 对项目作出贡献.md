title: chapter 6.2 - GitHub 对项目作出贡献
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - GitHub

---

向一个项目贡献内容，主要包括 Fork 派生项目副本和 Pull Request 合并请求两部分内容。

<!--more-->

## Fork 派生项目

![](https://git-scm.com/book/en/v2/images/forkbutton.png)

如果想要参与某个项目，但是没有推送权限，这时可以对这个项目进行“派生”。 

派生的意思是指，GitHub 将在你的空间中创建一个完全属于你的项目副本，且你对其具有推送权限。

通过派生，项目的管理者不再需要忙着把用户添加到贡献者列表并给予他们推送权限。 

人们可以派生这个项目，将修改推送到派生出的项目副本中，并通过创建合并请求（Pull Request）来让他们的改动进入源版本库。

创建了合并请求后，就会开启一个可供审查代码的板块，项目的拥有者和贡献者可以在此讨论相关修改，直到项目拥有者对其感到满意，并且认为这些修改可以被合并到版本库。

fork
> 在以前，“fork”是一个贬义词，指的是某个人使开源项目向不同的方向发展，或者创建一个竞争项目，使得原项目的贡献者分裂。 在 GitHub，“fork”指的是你自己的空间中创建的项目副本，这个副本允许你以一种更开放的方式对其进行修改。

**不必总是fork**
> 你可以在同一个版本库中不同的分支提交合并请求：如果你正在和某人实现某个功能，而且你对项目有写权限，你可以推送分支到版本库，并在 master 分支提交一个合并请求并在此进行代码审查和讨论的操作。不需要进行“Fork”。也就是没有推送权限时才派生项目副本再发出合并请求，有权限就直接使用分支发出合并请求。

## GitHub 流程

GitHub 设计了一个以合并请求为中心的特殊流程，流程通常如下：

1. 从 master 分支中创建一个新分支
2. 提交一些修改来改进项目
3. 将这个分支推送到 GitHub 上
4. 创建一个合并请求
5. 讨论，根据实际情况继续修改
6. 项目的拥有者合并或关闭你的合并请求

和 **chapter 5.1 分布式工作流程** 中的 **集成管理者工作流** 的流程差不多：

1. 项目维护者推送到主仓库。 
1. 贡献者克隆此仓库，做出修改。 
1. 贡献者将数据推送到自己的公开仓库。 
1. 贡献者给维护者发送邮件，请求拉取自己的更新。 
1. 维护者在自己本地的仓库中，将贡献者的仓库加为远程仓库并合并修改。 
1. 维护者将合并后的修改推送到主仓库。

只是可以使用 GitHub 提供的网页工具来代替邮件交流和审查修改。

### 合并请求举例

还没有完成

### 引用合并请求和议题

#### 编号引用

所有的合并请求和议题在项目中都会有一个独一无二的编号，就是说不会同时存在 3 号合并请求和 3 号议题。

引用语法：
* 当前fork：`#编号`
* 其他人对同一版本库的fork：`用户名#编号`
* 其他人不同版本库：`用户名/库名#编号`

![](https://www.git-scm.com/book/en/v2/images/mentions-01-syntax.png)

#### SHA引用

还可以通过 SHA-1 来引用提交，但必须完整的写出 40 位长的 SHA，GitHub 会在评论中自动地产生指向这个提交的链接。

## GitHub 风格的 Markdown

### 任务列表

在合并请求中，任务列表经常被用来在合并之前展示这个分支将要完成的事情。

#### 实现

Markdown

```
- [X] 编写代码
- [ ] 编写所有测试程序
- [ ] 为代码编写文档
```

效果

![task list](https://www.git-scm.com/book/en/v2/images/markdown-02-tasks.png)

只需要点击复选框，就能更新评论 —— 不需要修改 Markdown。

#### 特点

GitHub 还会将议题和合并请求中的任务列表整理起来集中展示。

例如一个合并请求中有任务清单，则在所有合并请求的总览页面上看到它的进度。

这使得人们可以把一个合并请求分解成不同的小任务，同时便于其他人了解分支的进度。

![task summary](https://www.git-scm.com/book/en/v2/images/markdown-03-task-summary.png)


### 摘录代码

```java
for(int i=0 ; i < 5 ; i++)
{
   System.out.println("i is : " + i);
}
```

![codeblock](https://www.git-scm.com/book/en/v2/images/markdown-04-fenced-code.png)

### 引用

```
> Whether 'tis Nobler in the mind to suffer
> The Slings and Arrows of outrageous Fortune,
```

![quote](https://www.git-scm.com/book/en/v2/images/markdown-05-quote.png)

快捷键：选中要引用的文字，然后按 `r` 键

### 表情符号

输入 `:` 后会自动显示表情符号

```
I :eyes: that :bug: and I :cold_sweat:.

:trophy: for :microscope: it.

:+1: and :sparkles: on this :ship:, it's :fire::poop:!

:clap::tada::panda_face:
```

![quote](https://www.git-scm.com/book/en/v2/images/markdown-07-emoji.png)

表情列表：www.emoji-cheat-sheet.com
