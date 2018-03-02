---
title: chapter 8.2 - Git 属性
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-Attributes
---

git 可以对特定的路径配置某些儿设置项，这些基于路径的设置项被称为 git 属性。

可以将 git 属性设置保存在 `~/.gitattributes` 文件里，或`.git/info/attributes` 文件里

<!--more-->

使用属性可以实现的功能：
1. 实现比较非文本文件
2. 对项目的文件或目录定义不同的合并策略
3. 在提交或检出前过滤内容

## 二进制文件

有些儿看似二进制文件，却希望进行比较，如 Word 文档，图片等。

有些儿看似文本文件，实质上却应被作为二进制文件处理，如 Xcode 项目的 `.pbxproj` 文件，是一个记录项目构建配置等信息的 JSON 格式的轻量级数据库，如果有两个人修改了它，通常无法合并内容，diff 的输出也帮不上什么忙。 它本应被机器处理。 因此，你想把它当成二进制文件对待。

Git 对待二进制文件 : 不会尝试转换或修正回车换行（CRLF）问题，运行 `git show` 或 `git diff` 时，也不会比较或打印文件的变化。

### 识别二进制文件

在 `.gitattributes` 文件里写入：

> *.pbxproj binary

### 比较二进制文件

比较二进制文件的秘诀在于告诉 Git 如何把二进制文件转换为普通文本文件，然后再使用普通的 diff 比较。

#### 比较 Word 文档

##### 1. 写入配置文件

在 `.gitattributes` 文件里写入：

> *.docx diff=word

表示所有 `.docx` 文件比较时都使用 `word` 过滤器

##### 2. 设置过滤器

2.1 下载并安装 docx2txt

> http://docx2txt.sourceforge.net

2.2 创建 docx2txt 脚本，作用是把输出结果包装成 git 支持的格式，内容如下：

> #!/bin/bash
> docx2txt.pl $1 -

2.3 给文件加可执行权限
```
$ chomod a+x docx2txt
```

2.4 配置 git 使用脚本
```
$ git config diff.word.textconv docx2txt
```

完成，再比较 word 文档就可以看到改动。

#### 比较图片

主要是比较大部分图像格式中都有的一种元数据 EXIF，包括了图片的高宽，占用空间等

下载安装转换图像为 EXIF 文本信息的程序 exiftool

```
$ echo '*.png diff=exif' >> .gitattributes

$ git config diff.exif.textconv exiftool
```

## 合并策略

可以通过属性对特定文件使用不同的合并策略，

一个非常有用的应用场景是在合并冲突时不去合并，而是直接使用你这边的版本，

例如把一个特性分支合并到主分支，但又不想在合并时弄乱主分支的数据库链接文件 database.xml：

文件 `.gitattributes` 里写入：

> database.xml merge=ours

定义虚拟的合并策略 ours：

```
$ git config --global merge.ours.driver true
```

则再合并时，database.xml 文件不会有冲突，而是保持主分支的版本

## 关键字展开



## 导出版本库

`export-ignore`

有些儿文件虽然在 git 库中，但导出归档时希望忽略掉，如有个测试文件夹 test 下所有文件都不归档，

则编辑 `.gitattributes` 文件，增加内容：

> test/ export-ignore

`export-subst`



