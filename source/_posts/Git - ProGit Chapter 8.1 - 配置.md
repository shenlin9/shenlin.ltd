title: chapter 8.1 - Git 设置
categories:
  - Git
  - Book-ProGit
tags:
  - Git

---

git 的设置项分为客户端设置和服务器端设置，都保存在 git 配置文件中，大部分属于客户端设置。

<!--more-->

## 设置文件

### 1.系统设置文件 gitconfig

    包含当前系统上每一个用户和每一个仓库的通用设置
    
    linux: /etc/gitconfig

    windows: git安装目录/etc/gitconfig
    
    --system 选项对应此文件
    
### 2.用户设置文件 .gitconfig

    只针对当前用户
    
    linux:  ~/.gitconfig 或 ~/.config/git/config

    windows: C:/users/$user/.gitconfig
   
    --global 选项对应此文件
    
### 3.当前库设置文件 config

    针对当前仓库
    
    当前项目/.git/config
    
    --local 选项对应此文件

**每一级的设置会覆盖上一级的设置**

## 读写设置文件

使用`git config`命令来读写设置文件

### 1. 列表设置

```
//系统设置文件
$ git config --system --list
$ git config --system --l

//用户设置文件
$ git config --global --list
$ git config --global --l

//当前库设置文件
$ git config --local --list
$ git config --local --l

//所有设置信息，会有重复项目
$ git config --list
$ git config --l
```

### 2. 单项设置

```
$ git config user.name
$ git config --get user.name
$ git config --unset user.name
$ git config --remove-section user
```

## 客户端常用设置项


### 1. 设置用户名和邮件地址

这是应该最先设置的信息，它们会被写入每一次的提交信息中
```
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

### 2. 设置文本编辑器

git 需要用户输入信息时会调用文本编辑器，

默认调用环境变量（$VISUAL 或 $EDITOR）设置的编辑器，

没有设置则使用 vi

```
$ git config --global core.editor vim
```

### 3. 设置提交信息模板

如果团队对提交信息的格式有要求，可以设置提交信息的模板格式

```
$ git config --global commit.template ~/.gitmessage.txt
```

### 4. 设置默认分页器

默认是 less，设置为 '' 将不使用分页一次性显示完毕

```
$ git config --global core.pager ''
```

### 5. 设置签署秘钥

设置 GPG 签署秘钥为设置项，创建签署的附注标签时则无需定义秘钥

```
$ git config --global user.signingkey <gpg-key-id>

$ git tag -s <tag-name>
```

### 6. 设置全局 .gitignore 文件

有些儿文件对于所有库都是需要忽略的，如 vim 的 `~` 结尾的文件，

虽然 `.gitignore` 文件可以包含不被跟踪的忽略文件，但仅对当前库有效，

`core.excludesfile` 则对全局有效

```
git config --global core.excludesfile ~/.gitignore_global
```

`~/.gitignore_global` 是自定义的文件，其格式要求和 `.gitignore` 文件一样

### 7. 自动模糊执行

git 输入命令错误会进行模糊匹配并提醒，如

```
$ git checkut dev
git: 'checkut' is not a git command. See 'git --help'.

Did you mean this?
        checkout
```

但并不会按模糊匹配的结果自动执行，若设置 `help.autocorrect` 则会自动执行

```
$ git config help.autocorrect 100

$ git checkut dev
WARNING: You called a Git command named 'checkut', which does not exist.
Continuing under the assumption that you meant 'checkout'
in 10.0 seconds automatically...
```
help.autocorrect 后面的参数单位是0.1秒，即10代表1秒，100代表10秒，设置为0则关闭

### 8. 设置输出颜色

#### 8.1 开启关闭颜色

git 默认会自动着色大部分输出，但也可以关闭输出

```
$ git config --global color.ui false
```

`color.ui` 取值 :

`false` 关闭着色

`auto`  默认值，直接输出到终端的内容会被着色，而当内容被重定向到一个管道或文件时，则不着色

`always` 永远着色，忽略终端和管道的不同

默认值 `auto` 已经满足大多数情况的需求，如果临时需要着色，还可以使用 `--color` 参数临时开启

#### 8.2 具体命令颜色

指定具体的命令是否显示颜色

`color.<command> <value>`

* `<command>` 为 git 子命令，如 branch, diff, status
* `<value>` 取值 false, true, always

```
$ git config --global color.diff always
```

设置某个命令的具体颜色和字体

`color.<command>.meta "<fg> <bg> <font>"`

* `<command>` 为 git 子命令，如 branch, diff, status
* `<fg>` 为前景色，取值：normal、black、red、green、yellow、blue、magenta、cyan、white
* `<bg>` 为背景色，取值：
* `<font>` 为字体，取值：bold、dim、ul（下划线）、blink、reverse（交换前景色和背景色）

```
$ git config --global color.diff.meta "blue black bold"
```

### 9. 回车换行

跨平台时会遇到 CRLF 问题。

这是因为 Windows 使用回车（CR）和换行（LF）两个字符来结束一行，而 Mac 和 Linux 只使用换行（LF）一个字符。

Git 有相关的回车换行转换设置。

在提交时自动地把回车和换行转换成换行，在检出代码时把换行转换成回车和换行（适用于 : windows 编辑器，git 服务器运行在 linux）
```
$ git config --global core.autocrlf true
```

在提交时自动把回车和换行转换成换行，在检出时不转换（适用于 : linux 编辑器，git 服务器运行在 linux，但需偶尔打开 windows 文件）
```
$ git config --global core.autocrlf input
```

关闭自动转换
```
$ git config --global core.autocrlf false
```

### A. 多余空白

git 提供了 6 个处理多余空白的选项

3 个默认打开的：

* trailing-space 包含下面两个
    * blank-at-eol，行尾的空格作为错误对待
    * blank-at-eof，文件底部的空行作为错误对待
* space-before-tab，行头 tab 前面的空格作为错误对待

3 个默认关闭的：

* indent-with-non-tab，以8个以上空格而非 tab 缩进的行作为错误对待（空格数由 tabwidth 选项控制）
* tab-in-indent，在行头表示缩进的 tab 作为错误对待
* cr-at-eol，行尾的回车是合法的

1 个辅助选项：

* tabwidth=<n> 指定 tab 的宽度，默认是 8，取值范围 1 到 63，和 `tab-in-indent`、`indent-with-non-tab` 关联

```
$ git config --global core.whitespace trailing-space,space-before-tab,indent-with-non-tab
```
* 设置时选项之间以逗号分隔
* 不指定的设置项即表示关闭，设置项前面加 `-` 也表示关闭

设置后影响的命令：

* git diff

    输出并着色时会注意到空白问题

* git apply

    ```
    //对有空白问题的补丁发出警告
    $ git apply --whitespace=warn <patch>

    //打补丁前自动修正空白问题
    $ git apply --whitespace=warn <patch>
    ```

* git rebase

    如果提交了有空白问题的文件，但还没推送到上游，可以让 Git 在重写补丁时自动修正它们

    ```
    git rebase --whitespace=fix
    ```

### B. 命令别名

Git 只是简单地将别名替换为对应的命令
```
$ git config --global alias.unstage 'reset HEAD --'
```
下面两条命令等价
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

执行外部命令，在命令前面加`!`
```
$ git config --global alias.visual '!gitk'
```
### C. 比较合并工具

#### C.1 使用预设工具

Git 预设了许多合并和解决冲突的工具，无需特别的设置就可以使用

查看预设合并工具、比较工具

```
$ git mergetool --tool-help
'git mergetool --tool=<tool>' may be set to one of the following:
                gvimdiff
                gvimdiff2
                gvimdiff3
                meld
                vimdiff
                vimdiff2
                vimdiff3
                winmerge

The following tools are valid, but not currently available:
                araxis
                bc
                bc3
                codecompare
                deltawalker
                diffmerge
                diffuse
                ecmerge
                emerge
                examdiff
                kdiff3
                opendiff
                p4merge
                tkdiff
                tortoisemerge
                xxdiff

Some of the tools listed above only work in a windowed
environment. If run in a terminal-only session, they will fail.

$ git difftool --tool-help
//和上面的的 mergetool 稍有不同
```

设置合并工具为 kdiff3

```
$ git config --global merge.tool kdiff3
```

#### C.2 使用预设外工具

Git 使用预设外工具稍微麻烦，需要通过全局包装脚本来传递参数和运行命令。

以 P4Merge 为例，首先下载 P4Merge 并安装，然后要编写一个全局包装脚本来运行命令。

Git 共传递了 7 个参数给 diff 工具：

    path old-file old-hex old-mode new-file new-hex new-mode

但 diff 和 merge 只需要 old-file 和 new-file 两个参数，

由包装 diff 的脚本来转发给 merge 的参数

##### C.2.1. 创建全局包装脚本

包装 diff 的脚本 : /usr/local/bin/extDiff 
```
$ cat /usr/local/bin/extDiff
#!/bin/sh
[ $# -eq 7 ] && /usr/local/bin/extMerge "$2" "$5"
```

使用脚本安装 merge 工具 : /usr/local/bin/extMerge
```
$ cat /usr/local/bin/extMerge
#!/bin/sh
/Applications/p4merge.app/Contents/MacOS/p4merge $*
```

使用包装脚本的好处是在改变比较和合并工具时很方便，

如 p4merge 改为 meld 直接修改脚本内容即可

##### C.2.2. 添加权限

为脚本添加可执行权限
```
$ sudo chmod +x /usr/local/bin/extMerge
$ sudo chmod +x /usr/local/bin/extDiff
```

##### C.2.3. 修改配置

使用命令行方式
```
//通知 Git 使用哪个合并工具
$ git config --global merge.tool extMerge

//规定命令运行的方式
$ git config --global mergetool.extMerge.cmd 'extMerge \"$BASE\" \"$LOCAL\" \"$REMOTE\" \"$MERGED\"'

//通知 Git 程序的返回值是否表示合并操作成功
$ git config --global mergetool.extMerge.trustExitCode false

//通知 Git 该用什么命令做比较
$ git config --global diff.external extDiff
```

或直接编辑 `~\.gitconfig` 文件
```
[merge]
  tool = extMerge
[mergetool "extMerge"]
  cmd = extMerge "$BASE" "$LOCAL" "$REMOTE" "$MERGED"
  trustExitCode = false
[diff]
  external = extDiff
```

##### C.2.4. 运行命令

这样的方式运行命令将直接启动 P4Merge
```
$ git diff 32d1776b1^ 32d1776b1
```

或在合并冲突时，手工启动 mergetool
```
$ git mergetool
```

## 服务器端设置


### 1. 检验对象有效性

git 可以检验对象的有效性以及 SHA-1 校验和是否保持一致，但比较耗费时间，特别是库很大或推送内容很大时，

默认关闭，开启后将在每次推送生效前检验库的完整性，可以避免被有问题的客户端破坏

```
$ git config --system receive.fsckObjects true
```

### 2. 禁用强制更新

如果推送到远程库的提交不是一次快进合并，则 git 默认禁止提交，但用户可以使用 `git push -f` 进行强制提交，

如果要禁用强制提交：

```
$ git config --system receive.denyNonFastForwards true
```

服务器端的钩子也可以实现，而且可以做到更精细的控制，如只禁止某一类用户非快进提交。

### 3. 禁止推送删除

有一些方法可以绕过 `denyNonFastForwards` 策略。

其中一种是先删除某个分支，再连同新的引用一起推送回该分支。

可以堵上这个漏洞：

禁止通过推送删除分支和标签，要删除标签则必须从服务器手动删除引用文件，

```
$ git config --system receive.denyDeletes true
```

通过用户访问控制列表（ACL）也能够在用户级的粒度上实现同样的功能

## 设置帮助

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
