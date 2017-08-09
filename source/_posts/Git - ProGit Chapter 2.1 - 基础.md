title: chapter 2.1 - Git 基础
categories:
  - Git
  - Book-ProGit
tags:
  - Git

---

## 创建 git 库

第一种是把现有项目或目录导入所有文件到 Git 中，
第二种是从一个服务器克隆一个现有的 Git 仓库

### 现有目录中初始化仓库

进入项目目录，执行
```
$ git init
```
将创建一个 git 仓库并进行初始化，
.git 目录就是 git 仓库，包含了 git 仓库必须的所有文件，
这时项目还没有文件被跟踪，再执行命令
```
$ git add -A
$ git commit -m "create project"
```
开始跟踪项目文件

### 克隆现有的仓库

```
$ git clone git@github.com:bitbite/gitgo.git

//自定义本地库的名字
$ git clone https://github.com/libgit2/libgit2 gitgo
```
* 将会创建目录 gitgo，并创建子目录 .git 保存项目所有文件

* 目标库的每一个文件的每一个版本都被拉取下来。

* 克隆的远程库的默认名字是 : origin

* 会自动设置本地 master 分支跟踪克隆的远程仓库的 master 分支


## 文件操作

### 查看文件状态

```
$ git status

// -s 选项输出格式更紧凑
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```
* 新添加的未跟踪文件前面有 ?? 标记
* 新添加到暂存区中的文件前面有 A 标记，
* 修改过的文件前面有 M 标记
 * M 有两个可以出现的位置
 * 右边的 M 表示该文件被修改了但是还没放入暂存区，
 * 左边的 M 表示该文件被修改了并放入了暂存区

### 查看文件修改内容

```
$ git diff
$ git diff --cached
$ git diff HEAD
```

### 跟踪新文件

```
$ git add README
```

### 暂存文件

```
$ git add README
```

### 忽略文件

要养成一开始就设置好 .gitignore 文件的习惯，以免将来误提交无用的文件

#### 1. .gitignore 的格式规范

* 所有空行或者以 ＃ 开头的行都会被 Git 忽略。
* 可以使用标准的 glob 模式匹配。
* 匹配模式可以以（/）开头防止递归。
* 匹配模式可以以（/）结尾指定目录。
* 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。

#### 2. glob 模式

glob 模式是指 shell 所使用的简化了的正则表达式 

* 星号（*）匹配零个或多个任意字符；
* [abc] 匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）
* 问号（?）只匹配一个任意字符；
* 方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 [0-9] 表示匹配所有 0 到 9 的数字）。
* 使用两个星号（\**) 表示匹配任意中间目录，比如`a/**/z` 可以匹配 a/z, a/b/z 或 `a/b/c/z`等。

举例：
```
# 不跟踪.a结尾文件
*.a

# 但是要跟踪lib.a结尾文件，即使有上面的规则
!lib.a

# 当前目录下的TODO文件，但不包含子目录下的TODO文件
/TODO

# build目录下的所有文件
build/

# doc目录(不包含子目录)下所有与的txt文件
doc/*.txt

# doc目录(包括子目录)下的所有pdf文件，
doc/**/*.pdf
```

### 提交更新

```
//将启动编辑器让你输入提交说明
//需要在启动的编辑器顶行输入提交说明并保存
//所有的#开头的注释行都会被删除
$ git commit

//在启动的编辑器中显示本次的修改
$ git commit -v 

//行内注释，不启动编辑器
$ git commit -m "modified"

//-a 选项将把**已跟踪**的文件自动加入暂存区并提交
//   省略了'git add'的步骤
$ git commit -am "modified something"
```

### 移除文件

#### 1. 移除文件
```
$ git rm file1
```
git 中移除文件要先从跟踪文件清单中移除然后从磁盘中移除
`git rm`即可同时完成上面两件事
等价于
```
$ rm file1
$ git add file1
```

#### 2. 强制移除已修改文件
```
$ git rm -f file1
```
删除之前已修改过或已放入暂存区，则必须要用强制删除选项 -f。 这是一种安全特性，用于防止误删还没有添加到快照的数据，这样的数据不能被 Git 恢复。

#### 3. 从跟踪文件清单中移除
```
$ git rm --cached file1
```
* 只从git仓库中删除，保留磁盘文件
* 如不小心把一堆日志文件加入暂存区时

#### 4. 使用 glob 模式
```
$ git rm log/\*.log

//移除~结尾的文件
$ git rm \*~
```

### 移动文件

git 并不跟踪文件移动操作

```
$ git mv file_from file_to
```
相当于
```
$ mv file_from file_to
$ git rm file_from
$ git add file_to
```
注意第一条不是 git 命令

## 撤销操作

git 中提交过的文件总是可恢复的，而未提交的可能永远丢失。

### 重新提交

提交后发现漏了文件，或者提交注释写的不好，想要对刚做的提交进行修正，可以使用`--amend`选项再次提交，则本次的提交对象将替换上次的提交对象，若暂存区的文件没有改动，则提交对象里的文件快照不变，若暂存区文件改动过，则文件快照也改变。

```
//只更新提交信息，生成新的提交对象，但树对象不变
$ git commit -m "make comment right" --amend

//补上漏掉的文件，生成新的提交对象，同时也生成新的树对象
$ git add index.html
$ git commit -m "make comment right" --amend
```

### 取消暂存

取消放入暂存区的文件
```
$ git reset HEAD file1
```

### 放弃修改

放弃工作目录中指定文件的修改
比较危险，当前修改过的文件会被从暂存区检出的文件覆盖
````
git checkout -- file1
```

