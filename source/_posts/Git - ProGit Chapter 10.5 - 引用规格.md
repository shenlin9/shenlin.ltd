title: chapter 10.5 - Git 引用规格
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-Refs

---

引用规格 Refspec 可以使用命令行的方式，或者写入`.git/config`配置文件，写入文件的引用规格会每次推送拉取时都被执行。

<!--more-->

## 引用规格

### 格式 

`[+]<src>:<dst>`

    \+    可选，表示在不能快进Fast Forward的时候也要强制更新引用
    src   源分支
    dst   目标分支

fetch 时，src 是远程库的分支，dst 是本地库的分支
push 时，src 是本地库的分支，dst 是远程库的分支
    
???拉取推送时的顺序问题???

    git clone [<options>] [--] <repo> [<dir>]
    
    git remote add [-t <branch>] [-m <master>] [-f] [--tags | --no-tags] [--mirror=<fetch|push>] <name> <url>
    
    git pull [<options>] [<repository> [<refspec>...]]
    
    git push [<options>] [<repository> [<refspec>...]]
    
    git fetch [<options>] [<repository> [<refspec>...]]
    git fetch [<options>] <group>
    git fetch --multiple [<options>] [(<repository> | <group>)...]
    git fetch --all [<options>]

### 通配符

在规格中使用**部分通配符**是不合法的
```
fetch = +refs/heads/qa*:refs/remotes/origin/qa*
```
这样是可以的
```
fetch = +refs/heads/qa/*:refs/remotes/origin/qa/*
```

## 通过引用规格获取远程库分支


### 通过命令生成引用规格

```
$ git remote add origin git@github.com:bitbiteme/gitgo
```
* `git remote add`命令将自动生成引用规格，并写入`.git/config`文件
* git 将获取远程服务器上`refs/heads/`下所有引用并写入`refs/remotes/origin/`中

### 通过 config 文件配置引用规格

```
$ cat .git/config
[remote "origin"]
        url = git@github.com:bitbiteme/gitgo
        fetch = +refs/heads/*:refs/remotes/origin/*
```
* remote "origin"   远程库在本地的名字
* url               远程库的地址
* fetch             引用规格refspec，仅针对 fetch 操作

### 引用规格举例

每次都拉取所有分支
```
fetch = +refs/heads/*:refs/remotes/origin/*
```

每次只拉取 master 分支
```
fetch = +refs/heads/master:refs/remotes/origin/master
```

将远程库的 master 分支拉入本地 `mymaster` 分支
```
fetch = +refs/heads/master:refs/remotes/origin/mymaster
```
或执行命令，命令行方式适用于只执行一次的操作
```
$ git fetch origin master:refs/remotes/origin/mymaster
```

指定多个引用规格
```
[remote "origin"]
	url = https://github.com/schacon/simplegit-progit
	fetch = +refs/heads/master:refs/remotes/origin/master
	fetch = +refs/heads/experiment:refs/remotes/origin/experiment
```
或 命令行一次拉取多个分支
```
$ git fetch origin master:refs/remotes/origin/mymaster \
	               topic:refs/remotes/origin/topic
	 
From git@github.com:schacon/simplegit
 ! [rejected]        master     -> origin/mymaster  (non fast forward)
 * [new branch]      topic      -> origin/topic
```
* master 分支被拒绝，因为不可快进，可通过前加 + 来覆盖


### 分支命名空间

如果项目工作流程复杂，包含了 QA 团队推送分支、开发人员推送分支、集成团队推送分支并且在远程分支上展开协作，可以为这些分支创建各自的命名空间

```
[remote "origin"]
	url = https://github.com/schacon/simplegit-progit
	fetch = +refs/heads/master:refs/remotes/origin/master
	fetch = +refs/heads/qa/*:refs/remotes/origin/qa/*
	fetch = +refs/heads/dev/*:refs/remotes/origin/dev/*
```

## 通过引用规格推送分支到远程库

QA 团队把他们的 master 分支推送到远程服务器的 qa/master 分支上
```
$ git push origin master:refs/heads/qa/master
```

或写入配置文件
```
[remote "origin"]
	url = https://github.com/schacon/simplegit-progit
	fetch = +refs/heads/*:refs/remotes/origin/*
	push = refs/heads/master:refs/heads/qa/master
```
每次直接
```
$ git push origin
```

## 通过引用规格删除远程库引用

通过引用规格删除远程服务器引用
```
$ git push origin :topic
```
因为引用规格（的格式）是 `<src>:<dst>`，
所以上述命令把 `<src>` 留空，
意味着把远程版本库的 topic 分支定义为空值，也就是删除它。

## 访问服务器上分支的提交记录
```
$ git log origin/master
$ git log remotes/origin/master
$ git log refs/remotes/origin/master
```
* 上面的 3 个命令效果相同
* 前两个命令都会被 git 扩展为最后一个命令的格式
