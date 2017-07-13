
# git 对象

标签（空格分隔）： AAA git 

---

## git 对象类型

共3种类型

1. blob    数据对象
    内容没有固定格式

2. tree    树对象
    内容有固定格式

3. commit  提交对象
    内容有固定格式

4. tag 附注标签对象

    标签分两种，一种是对象

## git 对象存储过程

通过 `irb` 命令启动 Ruby 的交互模式，查看字符串“what is up, doc?”的存储步骤：
```
$ irb
>> content = "what is up, doc?"
=> "what is up, doc?"
```

1. 构造头信息
头信息的组成格式：
`对象类型字符串("blob"|"tree"|"commit")` + `一个空格` + `数据内容的长度` + `一个空字节(null byte)`
```
>> header = "blob #{content.length}\0"
=> "blob 16\u0000"
```
2. 将上述头信息和原始数据拼接起来，并计算出这条新内容的 SHA-1 校验和。
```
>> store = header + content
=> "blob 16\u0000what is up, doc?"
>> sha1 = Digest::SHA1.hexdigest(store)
=> "bd9dbf5aae1a3862dd1526723246b20206e5fc37"
```
3. 调用zlib压缩新内容
```
>> zlib_content = Zlib::Deflate.deflate(store)
=> "x\x9CK\xCA\xC9OR04c(\xCFH,Q\xC8,V(-\xD0QH\xC9O\xB6\a\x00_\x1C\a\x9D"
```
4. 确定新内容的磁盘保存路径并创建目录
都保存在.git/objects/目录下
SHA-1校验和的前两个字符用于命名子目录
余下的 38 个字符则用作文件名
```
>> path = '.git/objects/' + sha1[0,2] + '/' + sha1[2,38]
=> ".git/objects/bd/9dbf5aae1a3862dd1526723246b20206e5fc37"
>> FileUtils.mkdir_p(File.dirname(path))
=> ".git/objects/bd"
```
5. 将经过zlib压缩的新内容写入文件
```
>> File.open(path, 'w') { |f| f.write zlib_content }
=> 32
```

概括：

* 对象类型、内容长度、原始内容拼接生成新内容，根据新内容生成SHA校验和确定磁盘存储路径，新内容再经过zlib压缩存放
* 每一条要存储的内容都要经过上述流程写入对应的一个文件
* 所有的git对象都以这种方式存储

这样存储的问题有：

* hash值和内容一一对应，没有存储文件名，记住每一个版本的hash值并不现实
* 不知道文件的组织结构
* 不知道是谁保存的，什么时候保存的，为什么保存
 
tree 树对象解决前两个问题
commit 提交对象解决最后一个问题

## blob 数据对象

### 存储数据对象

`hash-object`命令可存储任意文件到.git目录

#### 存储文件
```
$ git hash-object -w test.txt
5bdcfc19f119febc749eef9a9551bc335cb965e2
```
* `-w`选项表示将存储数据对象，否则只生成hash值

#### 存储字符串
```
$ echo "give this string a hash value" | git hash-object -w --stdin
582c37ab0e7750d6d162506ba876d0f6e0552f0c
```
`--stdin`表示从标准输入接收参数

### 查看数据对象

#### 定位存储位置
```
$ find .git/objects -type f
.git/objects/58/2c37ab0e7750d6d162506ba876d0f6e0552f0c
.git/objects/5b/dcfc19f119febc749eef9a9551bc335cb965e2
```

> 
Question:
键值对数据库和.git/objects/目录关系
后来改成键值对数据库了吗？
blob对象写入键值对数据库了吗？键名是hash值，键值是文件内容?

#### 查看对象类型
```
$ git cat-file -t 582c37ab0e7750d6d162506ba876d0f6e0552f0c
blob
```
* -t选项查看类型

#### 读取对象内容
```
$ git cat-file -p 582c37ab0e7750d6d162506ba876d0f6e0552f0c
give this string a hash value
```
* -p参数指示命令自动判断内容的类型，并显示格式友好的内容

#### 实现版本恢复

利用`hash-object`的存储数据对象和`cat-file`的读取对象内容的功能即可实现版本恢复：

```
//存储第一个版本
$ echo version1 > test.txt
$ git hash-object -w test.txt
5bdcfc19f119febc749eef9a9551bc335cb965e2

//存储第二个版本
$ echo version2 > test.txt
$ git hash-object -w test.txt
df7af2c382e49245443687973ceb711b2b74cb4a

//利用存储的版本恢复
$ cat test.txt
version2
$ git cat-file -p 5bdcfc19f119febc749eef9a9551bc335cb965e2 > test.txt
$ cat test.txt
version1
```

## tree 树对象

解决了文件名保存问题，解决了文件的组织结构问题。

tree对象和blob对象就是目录和文件的关系

### 查看 tree 对象

#### 方法一：根据提交记录查找到tree对象的hash值查看

先查看log记录，找到commit的hash值
```
$ git log --pretty=oneline
a52d1d8f7725c4adc427339ae1302920ab020d5c (HEAD -> dev) add monolog directory and txt
12c274b7d7d4d5a22bc815656e9054bb8123cee1 commit for tree test
29b3988a97dd904f9644949d6f974aae8d5adef1 add eeeeee
```
再查看commit对象里记录的顶层tree对象的hash值
```
$ git cat-file -p a52d1d8f7725c4adc427339ae1302920ab020d5c
tree 6f0c41495f324d9a54072b465dccc1e54e6fb634
parent 12c274b7d7d4d5a22bc815656e9054bb8123cee1
author bitbite <bitbite@foxmail.com> 1497309682 +0800
committer bitbite <bitbite@foxmail.com> 1497309682 +0800

add monolog directory and txt
```
最后根据tree对象的hash值查看内容
```
$ git cat-file -p 6f0c41495f324d9a54072b465dccc1e54e6fb634
100644 blob fd23bdb7c00d546fc05baf679ab071b09572f394    .gitignore
100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    4.txt
100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    5.txt
100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    6.txt
100644 blob 1d4e6ea39ca3b189bf698ccd5ed0eb689f3468ce    README.md
100644 blob d00491fd7e5bb6fa28c517a0bb32b8b506539d4d    test.txt
040000 tree 9ebe621674445e0931d92b36a62d78c758b0f624    vendor

```

#### 方法二：直接查看某分支最后一次提交对应的tree对象

格式：`分支名^{tree}`
```
$ git cat-file -p dev^{tree}
100644 blob fd23bdb7c00d546fc05baf679ab071b09572f394    .gitignore
100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    4.txt
100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    5.txt
100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    6.txt
100644 blob 1d4e6ea39ca3b189bf698ccd5ed0eb689f3468ce    README.md
100644 blob d00491fd7e5bb6fa28c517a0bb32b8b506539d4d    test.txt
040000 tree 9ebe621674445e0931d92b36a62d78c758b0f624    vendor
```
当然也可查看最后一次提交对应的commit对象
格式：`分支名^{commit}`
```
$ git cat-file -p dev^{commit}
tree 6f0c41495f324d9a54072b465dccc1e54e6fb634
parent 12c274b7d7d4d5a22bc815656e9054bb8123cee1
author bitbite <bitbite@foxmail.com> 1497309682 +0800
committer bitbite <bitbite@foxmail.com> 1497309682 +0800

add monolog directory and txt
```

### 创建 tree 对象

不能直接创建 tree 对象，需要通过暂存区来创建 tree 对象： Git 会根据某一时刻 index 区（暂存区）的状态创建并记录一个对应的树对象，所以要创建 tree 对象，可以先创建 index 区

#### 创建 index 区

使用`git ls-files`命令查看 index 区
```
$ git ls-files --stage
100644 43b67b58d92a592260065f61581b2ac26d20487f 0       test.txt
100644 fd23bdb7c00d546fc05baf679ab071b09572f394 0       .gitignore
100644 e69de29bb2d1d6434b8b29ae775ad8c2e48c5391 0       vendor/7.txt
```
使用`git add`之后可见 index 区被更新了
```
$ echo index file test > test.txt
$ git add test.txt

$ git ls-files --stage
100644 e18feaec5a521413c47872e9e3aab6101ef149cb 0       test.txt
100644 fd23bdb7c00d546fc05baf679ab071b09572f394 0       .gitignore
100644 e69de29bb2d1d6434b8b29ae775ad8c2e48c5391 0       vendor/7.txt
```
高层命令`git add`其实是调用底层命令`updaet-index`实现的 index 区更新
调用`update-index`将磁盘文件更新到 index 区：
```
$ git echo some new thing > test.txt
$ git touch wukong.txt && echo sun wukong is comig > wukong.txt
$ git update-index test.txt
$ git update-index --add wukong.txt
$ git status
On branch dev
Your branch is ahead of 'origin/dev' by 5 commits.
  (use "git push" to publish your local commits)
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   test.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        wukong.txt
```
wuong.txt是新建文件不在 index 区所以要加--add选项

也可将已 git 对象更新到 index 区：
```
$ git update-index --add --cacheinfo 100644,c0e38c69fd2e04533a0656757f1020a0a8f50698,wukong2.txt
```
    --cacheinfo 表示要添加的文件位于 git 数据库中，而不是当前目录，格式为
    --cacheinfo <mode>,<object>,<path>
        <mode>      文件模式
                    100644  普通文件
                    100755  可执行文件
                    120000  符号链接
        <object>    git对象SHA值
        <path>      文件名


#### 创建 tree 对象
```
$ git write-tree
f50fc22ff236284f2153395717adf238e184e4fd
```
返回的是 tree 对象

    --prefix 可以指定 tree 对象的上级

## commit 提交对象

提交 tree 对象同时会得到commit对象

```
$ git commit-tree -p 4bbb5c4b82eabe25ad426ccc9d0f889024389249 -m "this tree is commited by my hand" f50fc22ff236284f2153395717adf238e184e4fd

fd61fc5d10a311ad261c9b1a2ff22b09faf0a776
```
返回的是 commit 对象

    -p  上级 commit 对象，否则这次的 commit 对象不在git log的历史记录里
    -m  注释

## 一个完整的手工提交

```
//更改txt文件
$ echo this string is commited by hand > test.txt

//更新index区
$ git update-index test.txt

//创建tree对象
$ git write-tree
f50fc22ff236284f2153395717adf238e184e4fd

//查看日志找到最新的commit对象
$ git log --pretty=oneline -1
4bbb5c4b82eabe25ad426ccc9d0f889024389249 (HEAD -> dev) modify sth

//提交tree对象则会创建commit对象，
//-p参数指明commit对象的上级commit对象，
//否则这次的commit对象不在git log的历史记录里
$ git commit-tree f50fc22ff236284f2153395717adf238e184e4fd -p 4bbb5c4b82eabe25ad426ccc9d0f889024389249 -m "this tree is commited by my hand"
fd61fc5d10a311ad261c9b1a2ff22b09faf0a776

//当前index区还是更改状态
$ git status
On branch dev
Your branch is ahead of 'origin/dev' by 8 commits.
  (use "git push" to publish your local commits)
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   test.txt


//当前的日志还没有我们刚刚的提交
$ git log --pretty=oneline
4bbb5c4b82eabe25ad426ccc9d0f889024389249 (HEAD -> dev) modify sth
108adeba753504c992199d026e96902850fbf7e7 add some text
f26fdbdc4eb8d65dc14e70b89ae5ef9bdf600363 Initial commit

//原来还需要更新分支，指向当前最新的提交
$ git update-ref refs/heads/dev fd61fc5d10a311ad261c9b1a2ff22b09faf0a776

//再查看index区状态也正常了
$ git status
On branch dev
Your branch is ahead of 'origin/dev' by 9 commits.
  (use "git push" to publish your local commits)
nothing to commit, working tree clean

//日志也已经有了我们刚刚的提交
$ git log --pretty=oneline
fd61fc5d10a311ad261c9b1a2ff22b09faf0a776 (HEAD -> dev) this tree is commited by my hand
4bbb5c4b82eabe25ad426ccc9d0f889024389249 modify sth
108adeba753504c992199d026e96902850fbf7e7 add some text
f26fdbdc4eb8d65dc14e70b89ae5ef9bdf600363 Initial commit
```

上述过程就是`git add`和`git commit`的实际执行过程：

    update-index    将更改文件生成blob对象，并把blob对象注册到index区
    write-tree      根据index区内容创建tree对象
    commit-tree     根据tree对象创建commit对象
    update-ref      更新分支引用指向最新的commit对象




