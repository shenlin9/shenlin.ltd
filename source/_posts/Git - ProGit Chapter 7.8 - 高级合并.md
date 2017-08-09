title: chapter 7.8 - Git 高级合并
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-Merge

---

合并冲突时，Git 不会尝试智能的去解决冲突，Git 的哲学是聪明地决定无歧义的合并方案。 

<!--more-->

## 合并冲突

举例：文件 test1.txt 被某项目成员编辑后，所有 linux 的换行符都被替换为了 windows 的换行符，在合并时发生了冲突，

有哪些解决方案？

### 中断合并

发生冲突后，不想处理冲突也可以选择退出合并， `--abort` 将尝试恢复到运行合并前的状态
```
$ git merge --abort
```
* 只在发生冲突的合并起作用，合并顺利完成的无效
* 当运行命令前，在工作目录中有未储藏、未提交的修改时它不能完美处理，除此之外它都工作地很好


如果目录中没有需要保存的改动，也可以重置当前分支，注意会强制覆盖当前目录
```
$ git reset --hard HEAD
```

### 忽略空白

例子中的换行符属于空白符，空白符还包括空格、制表符等，可以在合并时选择忽略空白符

```
//忽略所有空白
$ git merge -X ignore-all-space feature

//忽略所有被修改的空白
$ git merge -X ignore-space-change feature

//忽略行尾空白
$ git merge -X ignore-space-at-eol
```
* -X 表示指定合并策略

### 手动合并

虽然合并时可以忽略空白造成的冲突，但可能造成文件里夹杂不同系统的换行，更好的方法是在合并前替换掉，如运行 dos2unix

首先进行合并，并且在合并时进入了冲突的状态
```
$ git merge spacebranch
```

合并冲突时，Git 在索引中保存了三方合并的所有版本，使用 `-u` 选项查看 Index 中的冲突文件
```
$ git ls-files -u
100644 97d865c851988b4c3d847ca06b75c826532c4278 1       test1.txt
100644 e34f756089792fb321ceece3a870cf849b46a146 2       test1.txt
100644 adc265b791250553a7f4e0689c5bda3d454e3955 3       test1.txt
100644 64d3997d44fc01e83c4ba158aaa728daeebf1963 1       test2.txt
100644 792d0a036ba6325e1d85bfa60dd2d747e9fab5d9 2       test2.txt
100644 925bdb5fd20d46300d8a5dcd9cde300dbffb1427 3       test2.txt
```
* 编号 1 的为三方合并的共同祖先版本
* 编号 2 的为我们的版本
* 编号 3 的为被合并的版本

可以使用下列语法查看冲突文件内容
```
$ git show :1:test1.txt
$ git show :2:test1.txt
$ git show :3:test1.txt
```
* :1:test1.txt 是查找对应 blob 对象 SHA-1 值的简写

将冲突文件内容释放出一份拷贝
```
$ git show :1:test1.txt > test1.common.txt
$ git show :2:test1.txt > test1.ours.txt
$ git show :3:test1.txt > test1.theirs.txt
```

手工修复空白问题
```
$ dos2unix test1.theirs.txt
```

手工三方合并文件
```
$ git merge-file -p test1.ours.txt test1.common.txt test1.theirs.txt > test1.txt
```

合并完成后，在提交之前，可以查看合并结果和三方版本的区别
```
//拿合并结果和我们的分支进行比较，也就是看**合并会引入什么内容**
$ git diff --ours

//拿合并结果和他们的分支进行比较，也就是看**合并的结果和他们那边的有什么不同**
$ git diff --theirs -b

//拿合并结果和共同祖先比较，也就是看**文件被我们的和他们的共同做了哪些儿修改**
$ git diff --base -b
```
* -b 表示去除空白，因为这里的比较都是使用的 Git 里保存的有空白的版本，不是我们导出来的去除空白的版本，为防止空白干扰去掉

清理为手工合并创建的临时文件
```
$ git clean -f
```

### 检出冲突

### 合并日志

### 组合式差异格式


## 撤销合并

### 修复引用

### 还原提交


## 其他类型合并

### 我们的和他们的偏好

### 子树合并





