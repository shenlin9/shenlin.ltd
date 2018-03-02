---
title: chapter 7.A - Git 调试
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-Blame
  - Git-Bisect
---

主要介绍两个子命令 blame 和 bisect：

> 当知道问题是在哪里引入的情况下文件标注 blame 可以帮助查找问题。

> 如果不知道哪里出了问题，并且自从上次可以正常运行到现在已经有数十个或者上百个提交，这个时候可以使用 bisect 来帮助查找， bisect 命令会对提交历史进行二分查找来尽快找到是哪一个提交引入了问题。

<!--more-->

## 文件标注

### 查看文件每一行的最后一次提交

```
$ git blame -L 8,18 _config.yml
498480f7 (iissnan  2015-10-22 23:00:12 +0800  8) # Set default keywords (Use a comma to separate)
d2b9152c (shenlin9 2017-07-17 16:05:50 +0800  9) keywords: "Programmer, Blog"
498480f7 (iissnan  2015-10-22 23:00:12 +0800 10)
498480f7 (iissnan  2015-10-22 23:00:12 +0800 11) # Set rss to false to disable feed link.
498480f7 (iissnan  2015-10-22 23:00:12 +0800 12) # Leave rss as empty to use site's feed link.
498480f7 (iissnan  2015-10-22 23:00:12 +0800 13) # Set rss to specific value if you have burned your feed already.
498480f7 (iissnan  2015-10-22 23:00:12 +0800 14) rss:
498480f7 (iissnan  2015-10-22 23:00:12 +0800 15)
498480f7 (iissnan  2015-10-22 23:00:12 +0800 16) # Specify the date when the site was setup
ffbf29f8 (shenlin9 2017-07-17 15:26:45 +0800 17) since: 2017
498480f7 (iissnan  2015-10-22 23:00:12 +0800 18)
```
* -L 表示行数
* SHA 值前面如果有 ^ 则表示该行还是文件第一次提交时的行，也就是提交后没有被改动过

### 找出代码的原始出处

Git 不会显式地记录文件的重命名，它会记录快照，然后在事后尝试计算出重命名的动作。
这其中有一个很有意思的特性就是可以让 Git 找出所有的代码移动。 `git blame -C` 会分析正在标注的文件，并且尝试找出文件中代码片段的原始出处。

```
$ git blame -C -L 1,20 "chapter 8.2 - Git 属性.md"
151bef1f (shenlin9 2017-08-04 17:01:31 +0800  1) title: chapter 8.2 - Git 属性
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800  2) categories:
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800  3)   - Git
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800  4)   - Book-ProGit
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800  5) tags:
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800  6)   - Git
2b6aa7f4 (shenlin9 2017-08-01 14:55:50 +0800  7)   - Git-Attributes
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800  8)
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800  9) ---
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800 10)
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800 11) git 可以对特定的路径配置某些儿设置项，这些基于路径的设置项被称为 git 属性。
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800 12)
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800 13) 可以将 git 属性设置保存在 `~/.gitattributes` 文件里，或`.git/info/attributes` 文件里
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800 14)
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800 15) <!--more-->
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800 16)
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800 17) 使用属性可以实现的功能：
2b6aa7f4 (shenlin9 2017-08-01 14:55:50 +0800 18) 1. 实现比较非文本文件
2b6aa7f4 (shenlin9 2017-08-01 14:55:50 +0800 19) 2. 对项目的文件或目录定义不同的合并策略
da89f3d6 (shenlin9 2017-07-31 18:28:24 +0800 20) 3. 在提交或检出前过滤内容
```
* 原始出处 SHA 对应的都是 commit 对象，并不是哪个文件
* 通常来说，你会认为复制代码过来的那个提交是最原始的提交，因为那是你第一次在这个文件中添加了这几行。 但 Git 会告诉你，你第一次写这几行代码的那个提交才是原始提交，即使这是在另外一个文件里写的。

## 二分查找
 
这是一个可以帮助你在几分钟内从数百个提交中找到 bug 的强大工具。

### 流程举例

> ... --> A --> B --> C --> D --> E --> F --> G

7 个提交历史，代码运行在 A 处是正常运行状态，在 G 处是错误状态，但不知道错误是在哪个提交被引入的，可以使用 bisect 进行二分查找

```
//先要启动 bisect
$ git bisect start

//告诉 bisect 哪个提交运行时有错误，没有参数则表示当前提交
$ git bisect bad G

//再告诉 bisect 哪个提交运行时是正确的
$ git bisect A
    //bisect 计算出 A 和 G 之间有 5 个提交，于是检出中间的提交 D 供你测试是否有错

//测试没有问题，则要告诉 bisect ，也表示错误在后面的 D 和 G 之间，则 bisect 继续检出 E 或 F 供你测试
$ git bisect good

//继续测试 bisect 检出的，如果有问题，说明找到了出错的提交，再告诉 bisect
$ git bisect bad

//bisect 根据目前的信息已经可以确定引入问题的位置在哪里，它会告诉你第一个错误提交的 SHA-1 值并显示一些提交说明，以及哪些文件在那次提交里修改过

//重置 HEAD 指针到最开始的位置
$ git bisect reset
```

### 自动化实现

通过设定一个脚本在项目正常的情况下返回 0，在不正常的情况下返回非 0，可以使 git bisect 自动化这些操作。 

首先，设定好项目正常以及不正常所在提交的二分查找范围
```
$ git bisect start HEAD v1.0
```
* 第一个参数是项目不正常的提交，第二个参数是项目正常的提交。

然后设定 Git 在每个被检出的提交里要自动执行的脚本，会直到找到第一个项目不正常的提交
```
$ git bisect run test-error.sh
```
* 也可以执行 make 或者 make tests 或者其他东西来进行自动化测试
* ??? make 和 make tests 木有了解


