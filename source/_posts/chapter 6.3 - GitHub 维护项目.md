title: chapter 6.3 - GitHub 维护项目
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - GitHub

---

在 GitHub 上创建、维护、管理自己的项目。

<!--more-->

## 创建库

### 创建

![newrepo](https://www.git-scm.com/book/en/v2/images/new-repo.png)

![newrepoform](https://www.git-scm.com/book/en/v2/images/newrepoform.png)

只有库名是必填的

### 访问

GitHub 上的项目可通过 HTTP 或 SSH 访问，格式是：

HTTP ： `https://github.com/<user>/<project_name>`

SSH ： `git@github.com:<user>/<project_name>`

通常对于公开项目可以优先分享基于 HTTP 的 URL，因为用户克隆项目不需要有一个 GitHub 帐号。

如果你分享 SSH URL，用户必须有一个帐号并且上传 SSH 密钥才能访问你的项目。 HTTP URL 与你贴到浏览器里查看项目用的地址是一样的。

## 添加合作者

版本库Settings --> Collaborators --> 根据用户名添加合作者，或者在合作者选择公开 email 的情况下，可以使用 email 添加

## 管理合并请求

合并请求可以来自 fork 的仓库副本的一个分支，或者同一仓库的另一个分支。 唯一的区别是 fork 过来的通常是和你不能互相推送的人，而内部的推送通常都可以互相访问。

合并请求有打开和关闭的状态，被 merge 后会自动关闭，或者库的管理者拒绝合并请求手动关闭。

### 邮件通知

举例：库的管理者是：tonychacon，库是 fade, 合并请求者是：schacon，schacon 发起合并请求后，tonychacon 收到的系统提醒邮件：

![pull-request-email](https://www.git-scm.com/book/en/v2/images/maint-01-email.png)

`git pull <url> <branch>`
```
$ git pull https://github.com/schacon/fade patch-1
```
这是一种合并远程分支的简单方式，无需必须添加一个远程分支；

或者这种方式
```
$ curl http://github.com/tonychacon/fade/pull/1.patch | git am
```

diff 和 patch 的标准版本
```
https://github.com/tonychacon/fade/pull/1.patch
https://github.com/tonychacon/fade/pull/1.diff
```

### 在合并请求上进行合作

??? 这里的几种合并方法要测试

库的管理者接收合并请求方法：
* 把代码拉取下来在本地进行合并
* 用 `git pull <url> <branch>` 语法
* 把 fork 添加为一个 remote，然后进行抓取和合并。
* 对于很琐碎的合并，也可以用 GitHub 网站上的 “Merge” 按钮。
    * 它会做一个 “non-fast-forward” 合并，即使可以快进（fast-forward）合并也会产生一个合并提交记录。 就是说无论如何，只要点击 merge 按钮，就会产生一个合并提交记录

![merge](https://www.git-scm.com/book/en/v2/images/maint-02-merge.png)

### 合并请求引用

如果有许多合并请求要处理，而又不想低效的添加一堆 remote 或者每次都要做一次拉取，可以使用下面的技巧简化操作。

使用`ls-remote`命令可以获得一个远程版本库里的所有引用：分支、标签和其它引用
```
$ git ls-remote origin
d5cd306f749c93114a2ab2b010d5d9e08a30ef15        HEAD
160326be301cc11e29effc30403481d5975a7083        refs/heads/gh-pages
d5cd306f749c93114a2ab2b010d5d9e08a30ef15        refs/heads/master

$ git ls-remote git@github.com:shenlin9/shenlin.ltd
d5cd306f749c93114a2ab2b010d5d9e08a30ef15        HEAD
160326be301cc11e29effc30403481d5975a7083        refs/heads/gh-pages
d5cd306f749c93114a2ab2b010d5d9e08a30ef15        refs/heads/master
```

当远程版本库中有打开的合并请求时会有 `refs/pull` 开头的引用
```
$ git ls-remote https://github.com/schacon/blink
10d539600d86723087810ec636870a504f4fee4d	HEAD
10d539600d86723087810ec636870a504f4fee4d	refs/heads/master
6a83107c62950be9453aac297bb0193fd743cd6e	refs/pull/1/head
afe83c2d1a70674c9505cc1d8b7d380d5e076ed3	refs/pull/1/merge
3c8d735ee16296c242be7a9742ebfbc2665adec1	refs/pull/2/head
15c9f4f80973a2758462ab2066b6ad9fe8dcf03d	refs/pull/2/merge
a5a7751a33b7e86c5e9bb07b26001bb17d775d1a	refs/pull/4/head
31a45fc257e8433c8d8804e3e848cf61c9d3166c	refs/pull/4/merge
```
* 以 `refs/pull/` 开头的引用实际上就是合并请求分支：
    * 但因为它们不在 `refs/heads/` 中，所以被 GitHub 视为是一种“假分支”，
    * 默认情况下克隆时会被忽略，所以不会从服务器上克隆到本地库中，
    * 但它们在远程库中还是隐式的存在着，使用上面的方法就很容易访问到了它们。
* 每个合并请求有两个引用，如 `refs/pull/2/head` 和 `refs/pull/2/merge`
* 以 `/head` 结尾的引用指向的提交记录和合并请求分支中的最后一个提交记录是同一个
* 以 `/merge` 结尾的引用代表的是如果你在网站上按了 “merge” 按钮对应的提交记录。 这甚至让你可以在按按钮之前就测试这个合并。
* ??? 所以开启一个合并请求实质上就是向目标版本库中推送两个引用

例如有人在我们的 GitHub 版本库中开启了一个合并请求，他们的分支叫做 `bug-fix`，指向 `a5a775` 这个提交记录，我们的 GitHub 版本库中没有 `bug-fix` 分支（因为那是在他们的 fork 中），但我们的本地库可以下载我们的 GitHub 版本库中一个名为 `pull/<pr#>/head` 指向 `a5a775` 的引用，也就是说我们可以很容易地拉取每一个合并请求分支而不用添加一堆 remote :
```
$ git fetch origin refs/pull/958/head
From https://github.com/libgit2/libgit2
 * branch            refs/pull/958/head -> FETCH_HEAD
```
* 这告诉 Git：连接到 origin 这个 remote，下载名字为 refs/pull/958/head 的引用。
* 然后 Git 将下载构建那个引用需要的所有内容，然后把指针指向 `.git/FETCH_HEAD` 下面你想要的提交记录。
* 最后你可以用 `git merge FETCH_HEAD` 把它合并到你想进行测试的分支，但那个合并的提交信息看起来有点怪

但如果有很多的合并请求，分别对每个合并请求进行一次这样的操作还是很麻烦， 下面这种方法可以一次性抓取所有合并请求，并且在连接到远程库时保持更新

编辑 `.git/config` 文件，增加请求分支的引用设置：
```
[remote "origin"]
    url = https://github.com/libgit2/libgit2.git
    fetch = +refs/heads/*:refs/remotes/origin/*
    fetch = +refs/pull/*/head:refs/remotes/origin/pr/* //这行是请求分支的设置
```
再执行 fetch
```
$ git fetch
# …
 * [new ref]         refs/pull/1/head -> origin/pr/1
 * [new ref]         refs/pull/2/head -> origin/pr/2
 * [new ref]         refs/pull/4/head -> origin/pr/4
# …
```
* 所有的合并请求在本地像分支一样展现，它们是只读的，当你执行抓取时它们也会更新

现在在本地测试合并请求中的代码变得超级简单
```
$ git checkout pr/2
Checking out files: 100% (3769/3769)， done.
Branch pr/2 set up to track remote branch pr/2 from origin.
Switched to a new branch 'pr/2'
```

### 合并请求之上的合并请求

不仅可以在主分支或者说 master 分支上开启合并请求，实际上可以在网络上的任何一个分支上开启合并请求 ，甚至可以在另一个合并请求上开启一个合并请求。

你想在这个合并请求上做一些修改或者你不太确定这是个好主意，或者你没有目标分支的推送权限，你可以直接在合并请求上开启一个合并请求。

当你开启一个合并请求时，在页面的顶端有一个框框显示你要合并到哪个分支和你从哪个分支合并过来的。

如果你点击那个框框右边的 “Edit” 按钮，你不仅可以改变分支，还可以选择哪个 fork。

![target](https://www.git-scm.com/book/en/v2/images/maint-04-target.png)

## 提醒和通知

评论框里输入 `@`，系统会自动补全项目中合作者或贡献者的名字和用户名，发布评论后，对应的用户会收到通知。

![mention](https://www.git-scm.com/book/en/v2/images/maint-05-mentions.png)

在议题或合并请求中被 `@` 的用户会被自动订阅相关的议题、合并请求，有新的活动会持续收到提醒通知，用户也可以点击 `Unsubscribe` 取消订阅。

![unsubscribe](https://www.git-scm.com/book/en/v2/images/maint-06-unsubscribe.png)

### 通知页面

用户头像 --> Settings --> Notifications

如果同时打开了邮件和网页通知，则阅读邮件通知后，对应的网页通知也会同时被标记为已读。

### 网页通知

网页通知只能在 GitHub 上查看。

### 邮件通知

GitHub 在发送的邮件头中附带了很多元数据，对于设置过滤器和邮件规则非常有用：

```
To: tonychacon/fade <fade@noreply.github.com>
Message-ID: <tonychacon/fade/pull/1@github.com>
Subject: [fade] Wait longer to see the dimming effect better (#1)
X-GitHub-Recipient: tonychacon
List-ID: tonychacon/fade <fade.tonychacon.github.com>
List-Archive: https://github.com/tonychacon/fade
List-Post: <mailto:reply+i-4XXX@reply.github.com>
List-Unsubscribe: <mailto:unsub+i-XXX@reply.github.com>，...
X-GitHub-Recipient-Address: tchacon@example.com
```
* `Message-ID` 中的信息会以 `<user>/<project>/<type>/<id>` 的格式展现所有的数据。如果是议题那么 `<type>` 字段就会是 “issues” 而不是 “pull” 。
* `List-Post` 如果邮件客户端能够处理，就可以高效的地通过邮件客户端在列表中发贴。
* `List-Unsubscribe` 如果邮件客户端能够处理，就可以高效的通过邮件客户端取消相关订阅。

## 特殊文件

版本库中有一些儿文件对 GitHub 来说是特殊的，会特殊处理。

### README

可以是 GitHub 能识别的任何格式，如 README, README.md, README.asciidoc

GitHub 找到 README 文件后，会在项目的首页渲染出来

README 的内容一般是项目信息，包括：
* 项目的作用
* 如何配置与安装 
* 如何使用和运行的例子 
* 项目的许可证 
* 如何向项目贡献力量

### CONTRIBUTING

如果版本库有名为 `CONTRIBUTING` 的文件，扩展名任意，则在其他用户提交合并请求时，会给出提示：

![contrib](https://www.git-scm.com/book/en/v2/images/maint-09-contrib.png)

此文件作用就是让提交合并请求的用户在开始前了解项目一些儿准则，了解版本库需要的和不需要的各种调整。

## 项目管理

### 改变默认分支

版本库Settings --> Branches --> Default branch

默认分支是版本库的基础分支，是提交合并请求的默认目标分支，是提交代码的默认目标分支，也是被克隆后默认检出的分支

### 移交项目

有需要时，可以把项目移交给 GitHub 注册的其他用户或组织，但需要你在对方那里有创建新库的权限。

版本库Settings --> Options --> Danger Zone --> Transfer ownership

![contrib](https://www.git-scm.com/book/en/v2/images/maint-11-transfer.png)
