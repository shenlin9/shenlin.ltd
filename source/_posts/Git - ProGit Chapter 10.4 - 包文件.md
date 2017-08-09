title: chapter 10.4 - Git 包文件
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-包文件

---

## 松散对象

Git 最初向磁盘中存储对象时所使用的格式被称为松散（loose）对象格式，是相对于包文件（packfile）说的，即不在包文件中的对象就是松散对象。

## 包文件

Git 会不定时地自动运行一个叫做 “auto gc” 的命令将多个松散对象打包成一个称为“包文件（packfile）”的二进制文件，以节省空间和提高效率。

<!--more-->

## gc命令的运行时刻

### 1.版本库中有太多的松散对象
    大多数时候，“auto gc”命令运行不会有效果
    大约需要 7000 个以上的松散对象
    或超过 50 个的包文件才能让 Git 启动一次真正的 gc 命令
    可以通过修改 gc.auto 与 gc.autopacklimit 的设置来改动这些数值
    
### 2.手动执行 git gc 命令

    ```
    $ git gc --auto
    ```
    
### 3.向远程服务器执行推送时


## gc 命令的功能

### 1.移除与任何提交都不相关的陈旧对象。

没被添加到任何提交记录的文件，Git 认为它们是悬空（dangling）的，不会将它们打包进新生成的包文件中

### 2.收集所有松散对象并将它们放置到包文件中

git打包生成一个包文件和一个索引文件。
包文件包含了从文件系统中打包的所有松散对象的内容。
索引文件包含了包文件的偏移信息，我们通过索引文件就可以快速定位任意一个指定对象。 

Git 打包对象时，会查找命名及大小相近的文件，并只保存文件不同版本之间的差异内容。

通常文件的较新版本会被完整保存内容，而最初较老的版本反而以差异方式保存——这是因为大部分情况下需要快速访问文件的最新版本。

可以查看包文件，观察它是如何节省空间的：
```
$ git verify-pack -v .git/objects/pack/pack-978e03944f5c581011e6998cd0e9e30000905586.idx
f26fdbdc4eb8d65dc14e70b89ae5ef9bdf600363 commit 176 121 3494
f50fc22ff236284f2153395717adf238e184e4fd tree   248 208 3615
c4e0c24377fd1887539e1bafc81dc7a57b212e9a tag    142 127 572
b042a60ef7dff760008df33cee372b945b6e884e blob   22054 5799 1463
033b4468fa6b2a9547a70d88d1bbe8bf3f9ed0d5 blob   9 20 7262 1 \
  b042a60ef7dff760008df33cee372b945b6e884e
1f7a7a472abf3dd9643fd615f6da379c4acb3e3a blob   10 19 7282
non delta: 15 objects
chain length = 1: 3 objects
.git/objects/pack/pack-978e03944f5c581011e6998cd0e9e30000905586.pack: ok
```
033b4 这个数据对象引用了数据对象 b042a。 第三列显示的是各个对象在包文件中的大小，可以看到 b042a 占用了 22K 空间，而 033b4 仅占用 9 字节。

### 3.将多个包文件合并为一个大的包文件

### 4.把refs引用打包到一个单独的文件

若 git 引用目录文件如下
```
$ find .git/refs -type f
.git/refs/heads/experiment
.git/refs/heads/master
.git/refs/tags/v1.0
.git/refs/tags/v1.1
```
`git gc --auto`会把上述文件打包成一个文件 .git/packed-refs
```
$ cat .git/packed-refs
# pack-refs with: peeled fully-peeled
cac0cab538b970a37ea1e769cbbde608743bc96d refs/heads/experiment
ab1afef80fac8e34258ff41fc1b867c702daa24b refs/heads/master
cac0cab538b970a37ea1e769cbbde608743bc96d refs/tags/v1.0
9585191f37f7b0fb9444f35a9bf50de191beadc2 refs/tags/v1.1
^1a410efbd13591db07496601ebc7a059dd55cfe9
```
* 如果你更新了引用，Git 并不会修改这个文件，而是向 refs/heads 创建一个新的文件。 

* Git 会首先在 .git/refs 目录中查找指定的引用，然后再到 .git/packed-refs 文件中查找。 

* 开头的 ^ 号表示这行是被附注标签指向的commit对象，而上一行就是附注标签对象。

## 查看空间占用的命令

查看数据库占用空间
```
$ git gc
Counting objects: 63, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (33/33), done.
Writing objects: 100% (63/63), done.
Total 63 (delta 15), reused 62 (delta 15)
```

快速查看占用空间大小
```
$ git count-objects -v
count: 0
size: 0
in-pack: 63
packs: 1
size-pack: 8
prune-packable: 0
garbage: 0
size-garbage: 0
```
size-pack 数值的单位是KB












