# chapter 1.2: git 起步

tags ： AAA Book-ProGit git 

---

## 简介

### git 设计目标

* 速度
* 简单的设计
* 对非线性开发模式的强力支持（允许成千上万个并行开发的分支）
* 完全分布式
* 有能力高效管理类似 Linux 内核一样的超大规模项目（速度和数据量）

### git 特点

#### 1. 直接记录文件快照而非文件差异

大部分VCS系统（CVS、Subversion、Perforce、Bazaar 等等）以文件变更列表的方式存储信息，存储的是初始版本文件和文件随时间逐步累积的差异。

![初始版本和差异](https://git-scm.com/book/en/v2/images/deltas.png)

Git 更像是把数据看作是对小型文件系统的一组快照。 每次你提交更新，或在 Git 中保存项目状态时，它主要对当时的全部文件制作一个快照并保存这个快照的索引。 为了高效，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。 
Git 对待数据更像是一个 快照流。

![初始版本和差异](https://git-scm.com/book/en/v2/images/deltas.png)

#### 2. 几乎所有操作都是本地执行

本地磁盘就有项目的完整历史，绝大多数操作只需访问本地文件和资源。

#### 3. 保证数据完整性

所有数据在存储前都计算校验和，然后以校验和来引用，意味着不可能在 git 不知情的情况下更改文件和目录。

git 数据库中保存的信息都是通过哈希值来索引的，而不是通过文件名。

git 使用 SHA-1 来计算文件内容和目录结构的校验和，是一个 40 位长度的十六进制字符串，占用 20 个字节长度。

#### 4. 一般只添加数据

git 操作几乎只往 git 数据库增加数据，一旦提交数据到 git 数据库，就难以丢失数据。

### 三个工作区域

工作目录(Working Directory)
:   从 git 压缩数据库中提取出来的项目某个版本的内容，放在磁盘上供修改

暂存区域(Staging Area)
:   保存了下次将提交的文件列表，是一个独立的文件，一般位于.git/index，也被称作索引文件

git 仓库(Repository)
:   保存项目的元数据和对象数据的地方，是 git 最重要的部分

### 三个状态

已修改(modified)
:   只修改了文件，未做暂存文件等其他操作

已暂存(staged)
:   对一个修改的文件做了提交标记，将包含在下次提交快照中：根据文件新内容生成了 SHA-1 和 git 对象，并把 SHA-1 和文件名写入了暂存文件 index。

已提交(committed)
:   数据已安全的保存在本地的 git 数据库中：生成了 tree 对象和 commit 对象

### 工作流程

1. 在工作目录修改文件
2. 把修改的文件放入暂存区域
3. 根据暂存区域的修改文件生成整个项目快照，并把快照永久存储到 git 数据库

## 安装

### Windows安装

1. 安装 Git for Windows

    https://git-scm.com/download/win
    
    和 Git 是两个独立的项目

2. 或者安装 GitHub for Windows

### 源码安装

安装 Git 依赖库：curl、zlib、openssl、expat，还有libiconv

安装最小化的依赖包
```
$ sudo yum install curl-devel expat-devel gettext-devel \
           openssl-devel zlib-devel
```
为了能够添加更多格式的文档（如 doc, html, info），需要安装以下的依赖包：
```
  $ sudo yum install asciidoc xmlto docbook2x
  $ sudo apt-get install asciidoc xmlto docbook2x
```
编译安装
```
$ tar -zxf git-2.0.0.tar.gz
$ cd git-2.0.0
$ make configure
$ ./configure --prefix=/usr
$ make all doc info
$ sudo make install install-doc install-html install-info
```

## 配置

### 配置文件

#### 1.系统配置文件
    包含当前系统上每一个用户和每一个仓库的通用配置
    
    linux: /etc/gitconfig
    windows: git安装目录/etc/gitconfig
    
    --system 选项对应此文件
    
#### 2.用户配置文件
    只针对当前用户
    
   linux:  ~/.gitconfig 或 ~/.config/git/config
   windows: C:/users/$user/.gitconfig
   
    --global选项对应此文件
    
#### 3.当前库配置文件
    针对当前仓库
    
    当前项目/.git/config
    
    --local选项对应此文件

**每一级的设置会覆盖上一级的设置**

### 常用配置项

使用`git config`命令来读写配置文件

#### 1. 设置用户名和邮件地址

这是应该最先设置的信息，它们会被写入每一次的提交信息中
```
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

#### 2. 设置文本编辑器

git 需要用户输入信息时会调用文本编辑器，未设置则使用系统默认编辑器
```
$ git config --global core.editor vim
```

#### 3. 设置命令别名

* Git 只是简单地将别名替换为对应的命令
```
$ git config --global alias.unstage 'reset HEAD --'
```
则下面两条命令等价
```
$ git unstage fileA
$ git reset HEAD -- fileA
```
其他简化命令举例
```
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
```

* 执行外部命令，在命令前面加`!`
```
$ git config --global alias.visual '!gitk'
```

### 检查设置项

#### 1. 列表设置

```
//系统配置文件
$ git config --system --list

//用户配置文件
$ git config --global --list

//当前库配置文件
$ git config --local --list

//所有配置信息，会有重复项目
$ git config --list
```
#### 2. 检查某一项设置

```
$ git config user.name
```

## 寻求帮助

以命令`config`举例

显示可用选项
```
$ git config -h
```

打开命令手册（随同 git 安装的本地手册，无需联网）
```
$ git config --help
$ git help config
```

打开man帮助文件
```
$ man git-config
```
