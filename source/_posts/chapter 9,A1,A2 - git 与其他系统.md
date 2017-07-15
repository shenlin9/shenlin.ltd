# chapter 9,A1,A2 : git 与其他系统

tags ：AAA Book-ProGit gitk gui libgit2

---

## 1. Git 与其他 VCS

### 1.1 Git 与 Subversion

Git 提供了和 Subversion 双向桥接的工具 git svn

**git svn**
* 允许使用 Git 作为客户端连接到 Subversion
* 可以使用 Git 所有本地功能，新建、合并分支，暂存，变基等
* 可以像 Subversion 客户端一样连接到 Subversion 服务器
* 是连接 VCS 和 DVCS 的桥梁

### 1.2 Git 与 Mercurial


### 1.3 Git 与 Perforce


### 1.4 Git 与 TFS


## 2. Git 与 图形界面工具

Git 的原生环境是终端，所以没有什么事情是图形界面客户端可以做而命令行客户端不能做的，命令行始终是可以完全操控仓库并发挥出全部功能和最新功能的地方。

对于某些儿任务而言，纯文本并不是最佳的选择，一个图形化的界面可能更适合。

一些图形界面的开发者为了支持某种他认为高效的工作流程，经过精心挑选，只实现了 Git 功能的一个子集，因此不同的图形界面工具是针对各自的工作流程所量身定做的。

每种工具都有其特定的目的和意义，从这个角度来看，不能说某种工具比其它的更好。

gitk 和 git-gui 都是 git 自带的图形化工具，就是针对某种任务设计的工具的两个例子。 它们分别为了不同的目的（即查看历史和制作提交）而进行了精简，略去了用不到的功能。

### 2.1 gitk

基于`git log`和`git grep`用于查看历史记录的图形界面
```
$ cd /path/to/repo
$ gitk --all
```
* 大部分选项都直接传给 git log 执行了
* --all 最有用的一个选项, 告诉 gitk 尽可能地从任何引用查找提交并显示，而不仅仅是从 HEAD

![](https://git-scm.com/book/en/v2/images/gitk.png)

* 上方的窗口显示提交历史
    * 每个点代表一次提交，黄点表示 HEAD，红点表示尚未提交的本地变动
    * 线代表父子关系
    * 彩色方块用来标示引用。 
* 中间是一组用来搜索历史的控件。
* 下方的窗口用来显示当前选中的提交的具体信息；
    * 评论和补丁显示在左侧
    * 摘要显示在右侧

### 2.2 git gui

制作提交的图形界面
```
$ git gui
```
* 不必进入库目录，进入则直接打开当前库，不进入则要求新建库或选择打开库

![](https://git-scm.com/book/en/v2/images/git-gui.png)

* 左侧是索引区；
    * 未暂存的修改显示在上方，已暂存的修改显示在下方。
    * 单击文件名左侧的图标可以将文件在暂存状态与未暂存状态之间切换
    * 选中一个文件名来查看它的详情。
* 右侧窗口上方
    * 以 diff 格式来显示当前选中文件发生了变动的地方。
    * 你可以通过右击某一区块或行从而将这一区块或行放入暂存区。
* 右侧窗口下方
    * 写日志和执行操作的地方。
    * 在文本框中键入日志然后点击 “提交” 就和执行 git commit 的效果差不多。
    * 如果想修订上一次提交, 可以选中"修订"单选框，上次一提交的内容就会显示在 “暂存区”，然后可以简单的对修改进行暂存和取消暂存操作，更新提交日志，最后再次点击 “提交” 用这个新的提交来覆盖上一次提交。

### 2.3 GitHub 客户端

客户端下载
https://windows.github.com
https://mac.github.com

GitHub 客户端很好的展示了一个面向工作流程的工具应该是什么样子——专注于提升那些常用的功能及其协作的可用性，而不是实现 Git 的 所有 功能。

GitHub 客户端推荐的工作流程有时也被叫做 “GitHub 流”。

GitHub 客户端分为 Windows 版和 Mac 版，外观和操作体验一致。

![](https://git-scm.com/book/en/v2/images/github_win.png)

* 左侧是正在追踪的仓库的列表；通过点击左上方的 “+” 图标，你可以添加一个需要追踪的仓库（既可以是通过 clone，也可以从本地添加）。

* 中间是输入-提交区，你可以在这里输入提交日志，以及选择哪些文件需要被提交。 （在 Windows 上，提交历史就显示在这个区域的下方；在 Mac 上，提交历史有一个单独的窗口）

* 右侧是修改查看区，它会告诉你工作目录里哪些东西被修改了（译注：修改模式），或选中的提交里包括了哪些修改（译注：历史模式）。

* 右上角 “Sync” 按钮，主要通过这个按钮来进行网络上的交互。
    * push，fetch，merge，和 rebase 在 Git 内部是一连串独立的操作, 而 GitHub 客户端将这些操作都合并成了单独一个功能。
    * 你点击同步按钮时实际上会发生如下这些操作：
        * git pull --rebase。 如果上述命令由于存在合并冲突而失败，则会退而执行 git pull --no-rebase。
        * git push。

![](https://git-scm.com/book/en/v2/images/branch_widget_win.png)

* 在分支切换挂件中输入新分支的名称来完成创建

<br/>

### 2.4 其他图形界面工具

还有许许多多其它的图形化 Git 客户端，其中既有单一功能的定制工具，也有试图提供 Git 所有功能的复杂应用。

Git 的官方网站整理了一份时下最流行的客户端的清单
http://git-scm.com/downloads/guis

在 Git 的维基站点还可以看到一份更全的清单
https://git.wiki.kernel.org/index.php/Interfaces,_frontends,_and_tools#Graphical_Interfaces.

## 3. Git 与 IDE、Shell

### 3.1 Visual Studio

从 Visual Studio 2013 Update 1 版本开始可以直接使用内嵌的 Git 客户端。

Visual Studio 现在拥有一套着眼于任务的强大 Git 操作界面。 它包括线性的历史视图、diff 视图、远程仓库操作命令，以及其它很多功能。 

文档:
http://msdn.microsoft.com/en-us/library/hh850437.aspx 。

### 3.2 Eclipse

Eclipse 附带了一个名为 Egit 的插件，它提供了一个非常完善的 Git 操作接口。 这个插件可以通过切换到 Git 视图来使用：(Window > Open Perspective > Other…， 然后选择 “Git”）。

Egit 帮助文档：
单击菜单 Help > Help Contents，然后从内容列表中选择 “EGit Documentation” 节点

### 3.3 Bash

Git 附带了几个 Shell 的插件，但是这些插件并不是默认打开的

#### 命令自动补全

从 Git 源代码中复制文件`contrib/completion/git-completion.bash`到某个目录，并且将它的路径添加到`.bashrc`文件中，则在执行命令时无需打完按tab键自动补全命令

```
//自动补全为 checkout
$ git chec<tab>
```

#### 自定义提示符


从 Git 源代码中复制文件`contrib/completion/git-prompt.sh`到某个目录，添加下面内容到`.bashrc`文件中

```
. ~/git-prompt.sh
export GIT_PS1_SHOWDIRTYSTATE=1
export PS1='\w\$(__git_ps1 " (%s)")\$ '
```
* \w 表示打印当前工作目录
* \$ 打印 $ 部分的提示符（prompt）
* __git_ps1 " (%s)" 表示通过格式化参数符（%s）调用`git-prompt.sh`脚本中提供的函数

### 3.4 Zsh



### 3.5 Powershell


## 3. Git 与编程语言

Git 整合进应用程序，有三种选择：
1. 启动一个 shell 来使用 Git 的命令行工具；
2. 使用 Libgit2；
3. 使用 JGit（仅限 Java）。

### 3.1 命令行 Git

就是启动一个 shell 进程并在里面使用 Git 的命令行工具来完成任务。

**优点：**

* 支持所有的 Git 的特性。
* 也碰巧相当简单，因为几乎所有运行时环境都有一个相对简单的方式来调用一个带有命令行参数的进程。

**缺点：**

* 所有的输出都是纯文本格式。
     然而 Git 的输出偶尔会改变格式，如`git log --graph`会显示彩线，这时你将被迫去解析 Git 的输出，无效且易出错。
* 错误修复能力不足。 
    如果版本库被损毁，或者用户使用了一个奇怪的配置， Git 只会简单地拒绝执行后续操作。
* 进程管理增加复杂性。
    Git 要求在一个独立的进程中维护一个 shell 环境，这增加了复杂性。 协调许多类似的进程是对能力的极大挑战（尤其是在某些情况下，当不同的进程在访问相同的版本库时）。

### 3.2 Libgit2

Libgit2 致力于为其他程序使用 git 提供更好的 API，使用 C 语言实现，任何支持 C 语言绑定的编程语言都可以调用。

|开发语言|对应的 Libgit2 绑定|
|--|--|
|PHP|php-git|
|Python|pygit2|
|Ruby|Rugged|
|.Net & Mono|LibGit2Sharp|
|Objective-C|objective-git|
|Node.js|nodegit|
|Go|git2go|
|Perl|Git::Raw|
|C++ Qt|libqgit2|
|Erlang|Geef|
|Lua|luagit2|
|...|...|

官方网站：
https://libgit2.github.com/

API 文档：
https://libgit2.github.com/libgit2

一系列指南：
https://libgit2.github.com/docs


### 3.3 JGit

Java 程序调用 Git，可以使用 JGit 库，JGit 是一个由 Java 语言实现的功能相对齐全的 Git 库，由 Eclipese 维护。

官方网站
http://www.eclipse.org/jgit

JGit API 在线官方文档： http://download.eclipse.org/jgit/docs/latest/apidocs

JGit Cookbook：
https://github.com/centic9/jgit-cookbook

几个好的资源:
http://stackoverflow.com/questions/6861881 