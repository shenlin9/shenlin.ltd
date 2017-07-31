# chapter 8.2 : Git 属性

title:  Git 属性
categories:
  - Git
  - Book-ProGit
tags:
  - Git

---

git 可以对特定的路径配置某些儿设置项，这些基于路径的设置项被称为 git 属性。

可以将 git 属性设置保存在 `~/.gitattributes` 文件里，或`.git/info/attributes` 文件里

<!--more-->

使用属性可以实现的功能：

1. 对项目的文件或目录定义不同的合并策略
2. 实现比较非文本文件
3. 在提交或检出前过滤内容

## 
