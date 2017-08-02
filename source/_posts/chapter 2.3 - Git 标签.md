title: chapter 2.3 - Git 标签
categories:
  - Git
  - Book-ProGit
tags:
  - Git

---

## 标签操作

git 可以给历史中的某个提交打上标签，以示重要，比较代表性的是标记发布节点（v1.0等）

标签指向一个 commit 对象，并且始终指向这个 commit 对象，不会移动，这是和分支的不同，分支会移动。

<!--more-->

### 两种标签

标签分为轻量标签和附注标签。

轻量标签就是一个引用，直接指向 commit 对象。

附注标签指向的是一个标签对象，再根据标签对象里的 object 属性指向 commit 对象。

附注标签保存的内容：

    object  指向被标签对象的指针
    type    被标签对象类型
    tag     标签
    tagger  标签创建者信息和时间戳
    注释信息

通常建议创建附注标签，这样可以保存以上所有信息；

轻量标签建议作为一个临时的标签，或者因为某些原因不想要保存上述信息时使用

附注标签还可以使用 GNU Privacy Guard （GPG）签名与验证。

### 创建标签

```
//创建轻量标签
$ git tag 1.0   

//创建附注标签
$ git tag -a 1.1 -m "software go to 1.1"

//针对某次历史提交打标签
$ git tag -a 0.9 -m "software go to 0.9" 9fceb02

//创建带签名的附注标签，-a 换 -s 即可
$ git tag -s 1.2 -m "software release 1.2"

//验证附注标签的签名
$ git tag -v 1.2
```

轻量标签不要加 -m 注释等参数，即使加上也不会被记录

`-a` 选项表示创建附注标签

### 显示标签

```
//列出已有标签
$ git tag

//根据模式列出标签，必须加选项 -l
$ git tag -l '1.*'

//显示标签 1.0 对应的提交对象信息，若是附注标签还有标签本身的信息
$ git show 1.0
```

### 推送标签

默认情况，`git push`不会推送标签到远程库上，需要手动推送
```
//推送指定标签
$ git push origin 1.0

//推送全部标签
$ git push --tags
```

### 检出标签

标签不能像分支一样来回移动，所以不能真正检出
但可以在特定的标签上创建一个分支：`git checkout -b [branchname] [tagname]`

```
$ git checkout -b Version1 1.0
```

如果分支 Version1 又做了新提交，则分支 Version1 向前移动了，指向了新提交，但标签 1.0 仍指向原来的提交

Question:
检出和不能移动有关系？



