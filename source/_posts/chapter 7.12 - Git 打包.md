title: chapter 7.12 - Git 打包
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-Bundle

---

`git bundle` 命令会将 `git push` 命令所传输的所有内容打包成一个二进制文件，你可以将这个文件通过邮件或者闪存传给其他人，然后解包到其他的仓库中。

<!--more-->

打包
```
//打包分支
$ git bundle create repo.bundle HEAD feature
Counting objects: 91, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (71/71), done.
Writing objects: 100% (91/91), 13.13 KiB | 0 bytes/s, done.
Total 91 (delta 26), reused 25 (delta 4)

//打包提交区间
$ git bundle create repo.bundle master ^9a466c5
$ git bundle create repo.bundle origin/master..HEAD
$ git bundle create repo.bundle master ^origin/master
```
* HEAD 表示打包时包含 HEAD 引用，否则解包时需指明检出的分支

验证是否合法的 Git 包
```
$ git bundle verify repo.bundle
repo.bundle is okay
The bundle contains these 2 refs:
a1eb7204a419025c462fb2ae88dc2a6545aaa5f0 HEAD
a1eb7204a419025c462fb2ae88dc2a6545aaa5f0 refs/heads/feature
The bundle records a complete history.
```

克隆命令解包
```
$ git clone repo.bundle repo -b feature
Cloning into 'repo'...
Receiving objects: 100% (91/91), 13.13 KiB | 0 bytes/s, done.
Resolving deltas: 100% (26/26), done.
```
* -b feature 表示检出 feature 分支
* 打包时有包含 HEAD 引用则默认检出 HEAD 指向的分支

查看包里可以导入哪些儿分支
```
$ git bundle list-heads repo.bundle
a1eb7204a419025c462fb2ae88dc2a6545aaa5f0 HEAD
a1eb7204a419025c462fb2ae88dc2a6545aaa5f0 refs/heads/feature
```

从包里导入分支到本地库
```
$ git fetch repo.bundle feature:newfeature
From repo.bundle
 * [new branch]      feature    -> newfeature
```
