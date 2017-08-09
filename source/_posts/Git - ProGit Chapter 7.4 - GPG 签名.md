title: chapter 7.4 - Git GPG 签名
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git-GPG

---

从互联网上获取、更新版本库时，想要验证提交是不是来自于可信来源，Git 提供了几种通过 GPG 来签署和验证工作的方式。

<!--more-->

## GPG 秘钥

查看 GPG 秘钥
```
$ gpg --list-keys
/c/Users/ssl/.gnupg/pubring.gpg
-------------------------------
pub   2048R/5480D0FB 2017-06-16
uid                  bitbite (abiteofbit) <bitbite@foxmail.com>
sub   2048R/A1D18F9B 2017-06-16
```

生成秘钥
```
$ gpg --gen-key
```

设置签名秘钥
```
git config --global user.signingkey 5480D0FB 
```

## GPG 秘钥和标签

使用 `-s` 参数创建签名标签
```
$ git tag -s v1.5 -m 'my signed 1.5 tag'
```

显示标签同时会显示签名信息
```
$ git show v1.5
```

验证签名标签
```
$ git tag -v v1.4.2.1
```
* 需要签名人的公钥在你的钥匙链中

## GPG 秘钥和提交对象

使用 `-S` 选项创建签名的提交
```
$ git commit -a -S -m 'signed commit'
```

日志里查看和验证签名的提交
```
$ git log --show-signature -1
```

配置 git log 验证签名并将以 `%G?` 格式列在输出中
```
$ git log --pretty="format:%h %G? %aN  %s"

5c3386c G Scott Chacon  signed commit
ca82a6d N Scott Chacon  changed the version number
```

拉取/合并时检查提交的签名，没有签名或没通过则拒绝合并
```
$ git pull --verify-signatures br-feature

$ git merge --verify-signatures br-feature
```

使用 `-S` 选项签名合并时生成的合并提交
```
$ git merge --verify-signatures -S  signed-branch
```
