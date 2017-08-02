title: chapter 10.5 - Git 引用规格
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-Refs

---

引用规格可以使用命令行的方式，或者写入`.git/config`配置文件，写入文件的引用规格会每次推送拉取时都被执行。

<!--more-->

## 引用规格格式 

`[+]<src>:<dst>`

    \+    可选，表示在不能快进Fast Forward的时候也要强制更新引用
    src   表示远程版本库的引用
    dst   表示远程引用在本地对应的位置
    
???拉取推送时的顺序问题???

    git clone [<options>] [--] <repo> [<dir>]
    
    git remote add [-t <branch>] [-m <master>] [-f] [--tags | --no-tags] [--mirror=<fetch|push>] <name> <url>
    
    git pull [<options>] [<repository> [<refspec>...]]
    
    git push [<options>] [<repository> [<refspec>...]]
    
    git fetch [<options>] [<repository> [<refspec>...]]
    git fetch [<options>] <group>
    git fetch --multiple [<options>] [(<repository> | <group>)...]
    git fetch --all [<options>]


## 获取远程版本库的引用

从远程版本库获取引用

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
* fetch             引用规格refspec

### 引用规格举例

每次都拉取所有分支
```
fetch = +refs/heads/master:refs/remotes/origin/master
```

每次只拉取 master 分支
```
fetch = +refs/heads/master:refs/remotes/origin/master
```

将远程库的 master 分支拉入本地 mymaster 分支
```
fetch = +refs/heads/master:refs/remotes/origin/mymaster
```
或执行命令
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
或
命令行一次拉取多个分支
```
$ git fetch origin master:refs/remotes/origin/mymaster \
	               topic:refs/remotes/origin/topic
	 
From git@github.com:schacon/simplegit
 ! [rejected]        master     -> origin/mymaster  (non fast forward)
 * [new branch]      topic      -> origin/topic
```
* master 分支被拒绝，因为不可快进，可通过前加 + 来覆盖

在规格中使用部分通配符是不合法的
```
fetch = +refs/heads/qa*:refs/remotes/origin/qa*
```
这样是可以的
```
fetch = +refs/heads/qa/*:refs/remotes/origin/qa/*
```

## 将本地引用规格推送到远程版本库

执行命令
```
$ git push origin master:refs/heads/master
```

或配置文件
```
[remote "origin"]
	url = https://github.com/schacon/simplegit-progit
	fetch = +refs/heads/*:refs/remotes/origin/*
	push = refs/heads/master:refs/heads/master
```

## 删除引用

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
