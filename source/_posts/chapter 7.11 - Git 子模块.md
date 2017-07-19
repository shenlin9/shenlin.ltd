# chapter 7.11 : Git 子模块
标签: AAA Git Book-ProGit submodule

title: Git 子模块
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - submodule

---

Git 子模块可以让一个库在子目录里包含另外一个库，这两个库分别有自己独立的提交历史记录，不会互相混淆。
<!-- more -->
为方便叙述，把两个库分别称为主库和子库，对应的项目分别称为主项目和子项目。

Git 子模块常用于当项目需要外部依赖如第三方库时。

使用 `git submodule` 命令来检验、更新和管理子模块。

当`git clone`或`git pull`一个包含子模块的库时，默认情况下子库不会被检出到用户的工作目录。

`git submodule init` 和 `git submodule update` 子命令用于检出子库到用户工作目录。

子模块的组成：主库里有一个称为`gitlink`的树条目，链接了子库中一个特殊的提交对象。

主项目根目录下的 `.gitmodules` 文件里指定了子模块的逻辑名和从哪个URL克隆。可以通过逻辑名用本地库地址重写URL。

不要搞混了远程库 remotes 和子模块 submodules，远程库是同一个项目的另外一个库，子模块是独立于主项目的另一个完全不同的子项目，是完全独立的两个项目，所以不能在主项目内部修改子模块的内容。

如果想将主项目和子项目的历史合并，把它们作为一个整体项目对待，如同时克隆和检出，则可以使用`git subtree merge`策略，。

## 子模块配置文件

```
$ cat .git/config
[submodule "themes/hueman"]
        url = git@github.com:ppoffice/hexo-theme-hueman.git
        active = true
[submodule "themes/next"]
        url = git@github.com:iissnan/hexo-theme-next.git
        active = true

$ cat .gitmodules
[submodule "themes/hueman"]
        path = themes/hueman
        url = git@github.com:ppoffice/hexo-theme-hueman.git
[submodule "themes/next"]
        path = themes/next
        url = git@github.com:iissnan/hexo-theme-next.git
```

## 子块模命令详解

### 添加子模块

完整格式：

* `git submodule add [-b <branch>] [-f|--force] [--name <name>] [--reference <repository>] [--depth <depth>] [--] <repository> [<path>]`

常用格式：

* `git submodule add <repo> [<path>]`

把库`<repo>`添加为子模块，指定保存路径为`<path>`，并添加到暂存区准备提交，并把子模块信息写入`.gitmodules`文件和`.git/config`文件。

`<repo>`

* 必填
* 应是子模块的原始库 URL
* 可以是绝对路径或相对路径，相对路径必须以`./`或`../`开头
* 无论相对还是绝对路径，都会写入`.gitmodules`文件，被后续用户来使用。
* 如果是相对与主项目的相对路径，Git 会假定主项目和子项目是聚在一起的，并位于相同的相对位置，这时只需要主项目的URL，再根据`.gitmodules`文件中的相对路径就能正确的定位到子模块。
 
`<repository>` is the URL of the new submodule’s origin repository. This may be either an absolute URL, or (if it begins with ./ or ../), the location relative to the superproject’s default remote repository (Please note that to specify a repository foo.git which is located right next to a superproject bar.git, you’ll have to use ../foo.git instead of ./foo.git - as one might expect when following the rules for relative URLs - because the evaluation of relative URLs in Git is identical to that of relative directories).

The default remote is the remote of the remote tracking branch of the current branch. If no such remote tracking branch exists or the HEAD is detached, "origin" is assumed to be the default remote. If the superproject doesn’t have a default remote configured the superproject is its own authoritative upstream and the current working directory is used instead.

`<path>`

* 可选
* 值是子模块在主项目中的相对路径
* 也被用作子模块的逻辑名，除非使用`--name`选项指定了逻辑名
* 没有此参数则根据`<repo>`参数自动生成，如"/path/to/repo.git"生成"repo"
* 如果指定的路径已经存在且是一个有效的 git 库，则只把子模块添加到暂存区但不执行克隆，这种方法很容易从头开始创建全新子模块，创建完后需要推送到 URL 去。

例子：添加已存在的 git 库为子模块
```
//提示暂存区已有了
$ git submodule add git@github.com:iissnan/hexo-theme-next.git themes/next
'themes/next' already exists in the index

//从暂存区移除
$ git rm --cached themes/next

//再次添加则成功
$ git submodule add git@github.com:iissnan/hexo-theme-next.git themes/next
Adding existing repo at 'themes/next' to the index
```

### 查看子模块

完整格式：
`git submodule [--quiet] status [--cached] [--recursive] [--] [<path>…​]`

常用格式：
`git submodule`
`git submodule status`

显示子模块状态，会显示每个子模块当前检出提交的 SHA-1 值，子模块路径和 Git 对 SHA-1 的描述。

`--recursive` 递归显示嵌套的子模块

SHA-1 前缀含义：
`-` 子模块未初始化
`+` 子模块当前检出对应的提交对象 SHA-1 和子库暂存区里的 SHA-1 未匹配（if the currently checked out submodule commit does not match the SHA-1 found in the index of the containing repository）
`U` 子模块合并冲突

举例：
```
$ git submodule
 c88e232969ad224a1869d764b403b8ca763484c9 themes/hueman (v0.3.0-21-gc88e232)
 f597e3149fd529e577f76ccfc485f9d9fb367182 themes/next (v5.1.1-182-gf597e31)
 
 $ git submodule status themes/hueman
 c88e232969ad224a1869d764b403b8ca763484c9 themes/hueman (v0.3.0-21-gc88e232)
```

### 初始化子模块

完整格式：

* `git submodule [--quiet] init [--] [<path>…​]`

常用格式：

* `git submodule init [--] [<path>…​]`

功能描述：

* 使用文件`.gitmodules`中的设置作为模板，设置`.git/config`中`submodule.$name.url`，如果 URL 是相对路径，则使用默认的远程库解析，如果没有默认的远程库，则使用当前库。
* 此命令也会复制 `submodule.$name.update` 的值。
* 此命令不会修改 `.git/config` 中已存在的设置
* 可以先在 `.git/config` 文件里定义子模块的克隆 URL，然后再使用 `git submodule update`，如果不需要自定义子模块的 URL，也可以不显式的进行初始化，直接使用 `git submodule update --init`。

参数选项：

* `<path>`
    * 可选
    * 指定初始化的子模块
    * 没有指定此参数时，如果设置了`submodule.active`则初始化此子模块，否则初始化所有模块


### 注销子模块

完整格式：

* `git submodule [--quiet] deinit [-f|--force] (--all|[--] <path>…​)`

常用格式:

* `git submodule deinit <path>`
* `git submodule deinit --all`

功能描述：

* 移除子模块的工作目录，所以如果不想检出子模块工作目录时可使用此命令
* 从`.git/config`文件中移除全部`submodule.$name`部分，再次执行命令 `git submodule update`，`git submodule foreach`，`git submodule sync` 时则忽略所有未注册的模块
* 如果想从库中移除子模块使用命令 `git rm` 

参数选项：

* `<path>` 缺少此参数会报错
* `--all` 注销所有模块
* `-f|--force` 移除子模块的工作目录，即使进行了本地修改

例子：
```
$ git submodule deinit themes/hueman
Submodule work tree 'themes/hueman' contains a .git directory
(use 'rm -rf' if you really want to remove it including all of its history)

```

### 更新子模块

完整格式：

* `git submodule update [--init] [--remote] [-N|--no-fetch] [--[no-]recommend-shallow] [-f|--force] [--checkout|--rebase|--merge] [--reference <repository>] [--depth <depth>] [--recursive] [--jobs <n>] [--] [<path>…​]`

功能描述：

更新已注册的子模块：克隆子模块库和更新子模块工作目录

update 有多种执行方式，怎样执行由命令行选项和配置变量`submodule.<name>.update`这两项决定，命令行选项比配置变量优先级高，如果两者都没有设定，则执行`--checkout`

常用格式：

* `git submodule update <path>`
   
    不更新子模块
    <br/>

* `git submodule update --init`
 
    子模块未被初始化，使用文件 `.gitmodules` 中的配置初始化子模块
    <br/>

* `git submodule update --checkout`
* `git submodule update --f --checkout`
* `git submodule update --force --checkout`

    the commit recorded in the superproject will be checked out in the submodule on a detached HEAD.
    
    If --force is specified, the submodule will be checked out (using git checkout --force if appropriate), even if the commit specified in the index of the containing repository already matches the commit checked out in the submodule.
    <br/>
 
* `git submodule update --rebase`
 
    当前子模块的分支变基到主项目的提交对象
    the current branch of the submodule will be rebased onto the commit recorded in the superproject.
    <br/>

* `git submodule update --merge`

    主项目会被合并到子模块的当前分支
    <br/>

* `git submodule update --xxxx`
 
    xxxx 表示自定义命令，任何的 shell 命令都会带一个参数：主项目的提交对象的 SHA-1。

    只有 `submodule.<name>.update` 设置为 `!command` 时，自定义命令才有效，`!`后面的 command 就是自定义命令。

### 摘要

`git submodule [--quiet] summary [--cached|--files] [(-n|--summary-limit) <n>] [commit] [--] [<path>…​]`

显示提交摘要

Show commit summary between the given commit (defaults to HEAD) and working tree/index.

For a submodule in question, a series of commits in the submodule between the given super project commit and the index or working tree (switched by --cached) are shown.

If the option --files is given, show the series of commits in the submodule between the index of the super project and the working tree of the submodule .

`--files`
* 
* 不允许再使用`--cached`选项，不允许再显式的使用提交对象

`git diff --submodule=log` 同样的效果

### 在子模块上执行命令

`git submodule [--quiet] foreach [--recursive] <command>`

在每个检出的子模块上依次执行任意的 shell 命令，没有被检出的子模块将被忽略。

在任何子模块中执行命令被终端时将返回非0值，可以在命令结尾添加`||:`来覆盖

shell 命令可以访问下列变量：

* `$name`       是`.gitmodules`文件里的子模块名
* `$path`       是子模块相对于主项目的路径
* `$sha1`       是在主项目中记录的子项目的SHA1值
* `$toplevel`   是主项目的绝对路径

`--quiet` 在执行命令之前，不输出子模块的名称，默认是输出的
`--recursive` 递归执行


举例：输出每个子模块的路径和检出对象 SHA-1
```
$ git submodule foreach 'echo $path `git rev-parse HEAD`'
Entering 'themes/hueman'
themes/hueman c88e232969ad224a1869d764b403b8ca763484c9
Entering 'themes/next'
themes/next 403bbd06f7f00faf4c19fd8c12b52b5183a2807b
```


### 同步子模块的远程库URL到主项目

在子模块更改了自己的远程库 URL 后，主项目这时也要相应的更新`.gitmodules`文件中的设置，此命令即实现此功能。

完整格式：

* `git submodule [--quiet] sync [--recursive] [--] [<path>…​]`

功能描述：

* 同步子模块的远程库 URL 设置到文件 `.gitmodules` 里，只影响那些在文件 `.git/config` 里已经有了 URL 条目的子模块。

常用格式：

* `git submodule sync`
    同步所有子模块

* `git submodule sync --recursive`
    递归同步所有子模块包括嵌套的子模块

* `git submodule sync -- themes/next`
    只同步指定的模块`themes/next`

### 吸收子模块 git 目录

`git submodule [--quiet] absorbgitdirs [--] [<path>…​]`

    absorb  [əb'zɔːb;] 吸收

    把子模块 git 目录移动到主项目的`$GIT_DIR/modules`路径下，然后通过在主项目下添加指向子模块 git 目录的`.git` 文件和设置`core.worktree`变量来链接子模块的 git 目录和工作目录

    命令默认递归执行。
    
    If a git directory of a submodule is inside the submodule, move the git directory of the submodule into its superprojects `$GIT_DIR/modules` path and then connect the git directory and its working directory by setting the `core.worktree` and adding a .git file pointing to the git directory embedded in the superprojects git directory.

### 选项详解

#### `-q|--quiet`

    静默执行，只显示错误信息

#### `--all`

    只对 `deinit` 命令有效
    
    表示注销所有工作目录的子模块
    
#### `-f|--force`

    此选项只对 `add`, `deinit`, `update --checkout` 命令有效
    
    执行 `add` 时表示允许添加一个原本被忽略的子模块
    执行 `deinit` 时表示即使子模块工作目录有本地修改也移除
    执行 `update --checkout` 时表示子模块切换到另一个提交时将丢弃子模块的本地修改，并且总是在子模块里执行检出操作，即使子库暂存区中提交对象列表匹配子模块检出的提交对象。
    even if the commit listed in the index of the containing repository matches the commit checked out in the submodule.
    
#### `--cached`

    只对 `status` 和 `summary` 命令有效
    
    这两个命令默认是使用 HEAD 分支的提交对象，此选项则表示使用暂存区的提交对象

#### `--files`

    只对 `summary` 命令有效
    
    此选项表示比较 HEAD 分支的提交对象和暂存区的提交对象

#### `-n|--summary-limit`

    只对 `summary` 命令有效
    
    限制摘要里提交的数量
    0 表示不显示摘要，负数表示不限制数量（默认），限制只应用于修改的子模块
    
    The size is always limited to 1 for added/deleted/typechanged submodules.

#### `--remote`

    只对`update`命令有效
    
Instead of using the superproject’s recorded SHA-1 to update the submodule, use the status of the submodule’s remote-tracking branch. The remote used is branch’s remote (branch.<name>.remote), defaulting to origin. The remote branch used defaults to master, but the branch name may be overridden by setting the submodule.<name>.branch option in either .gitmodules or .git/config (with .git/config taking precedence).

This works for any of the supported update procedures (--checkout, --rebase, etc.). The only change is the source of the target SHA-1. For example, submodule update --remote --merge will merge upstream submodule changes into the submodules, while submodule update --merge will merge superproject gitlink changes into the submodules.

In order to ensure a current tracking branch state, update --remote fetches the submodule’s remote repository before calculating the SHA-1. If you don’t want to fetch, you should use submodule update --remote --no-fetch.

Use this option to integrate changes from the upstream subproject with your submodule’s current HEAD. Alternatively, you can run git pull from the submodule, which is equivalent except for the remote branch name: update --remote uses the default upstream repository and submodule.<name>.branch, while git pull uses the submodule’s branch.<name>.merge. Prefer submodule.<name>.branch if you want to distribute the default upstream branch with the superproject and branch.<name>.merge if you want a more native feel while working in the submodule itself.

#### `-N|--no-fetch`

    只对`update`命令有效
    
    表示不从远程库取回任何新对象

#### `--checkout`

    只对`update`命令有效

    默认检出主项目到子模块的分离的 HEAD
    
    当 `submodule.$name.update` 的值不是 `checkout` 时，此选项主要用来覆盖其值
    
    如果 `submodule.$name.update` 没有被显式的设置或设置为`checkout`，则此选项被隐式使用
    
#### `--merge`

    只对`update`命令有效

    把主项目合并到子模块的当前分支，子模块的 HEAD 不会分离，如果合并失败，需自己解决冲突。
    
    如果 `submodule.$name.update` 值是 `merge`，则此选项被隐式使用

#### `--rebase`

    只对`update`命令有效

    把子模块的当前分支变基到主项目，子模块的 HEAD 不会被分离，如果合并失败，需自己使用`rebase`解决.
    
    如果 `submodule.$name.update` 的值是 `rebase`，则此选项被隐式使用

#### `--init`

    只对`update`命令有效
    
    更新前初始化所有没有被 `git submodule init` 调用过的子模块

#### `--name`

    只对`add`命令有效
    设置子模块的名字（默认使用子模块路径作名字）
    名字的有效性规则和目录名的有效性规则是一样的，不能以/结尾

#### `--reference <repository>`

    只对`add`, `update`命令有效
    
    当上述命令需要自动克隆一个远程库时，自动把此选项传递给 `git clone` 命令
    
    注意：除非仔细阅读过 `git clone` 命令 `--reference` 和 `--shared` 选项的注意事项，否则不要使用这个选项

#### `--recursive`

    只对 `foreach`, `update`, `status`, `sync` 命令有效
    
    表示命令不仅仅针对当前库的子模块，也针对子模块中多层嵌套的子模块

#### `--depth`

    只对`add`, `update`命令有效
    
    创建一个浅克隆时截止到指定的版本次数

#### `--no-recommend-shallow | --recommend-shallow`

    只对`update`命令有效
    
    默认使用由文件 `.gitmodules` 推荐的 `submodule.<name>.shallow` 初始化克隆的子模块，忽略推荐则使用 `--no-recommend-shallow`

#### `-j <n>|--jobs <n>`

    只对`update`命令有效
    
    与多个作业任务并行克隆新的子模块，是 `submodule.fetchJobs` 默认值
    
#### `<path>…`

    表示子模块的路径，指定后则限制命令只能操作指定路径的子模块

## Q&amp;A


有两个子模块，为啥只显示一个？
```
$ ll .git/modules
drwxr-xr-x 1 ssl 197121 0 七月 18 10:15 hueman/
```

deinit 必须搭配 -f 参数吗
```
$ git submodule deinit themes/next
Submodule work tree 'themes/next' contains a .git directory
(use 'rm -rf' if you really want to remove it including all of its history)
```
https://stackoverflow.com/questions/23271424/issue-with-removing-a-git-submodule
貌似要先移除或改名子模块文件夹，然后再deinit

https://stackoverflow.com/questions/1260748/how-do-i-remove-a-submodule

https://stackoverflow.com/questions/16160993/moving-a-git-working-copy-containing-submodules/16161950#16161950

测试git clone 的子项目何时被添加到Index的？

git subtree？



---

changesets 是什么玩意？

    就是提交对象的部分集合
    
    [other history]   [ "changeset 1" ]
    o - o - o - o - o ( - o - o - o - o)
     \
      (o - o - o - o - o)
      [ "changeset 2" ]

如何将库中现有的子项目添加为子模块？
```
$ git submodule
fatal: no submodule mapping found in .gitmodules for path 'themes/hueman'

$ git submodule add git@github.com:ppoffice/hexo-theme-hueman.git ./themes/hueman
'themes/hueman' already exists in the index

$ cat .gitmoduless
cat: .gitmoduless: No such file or directory

$ git rm --cached themes/hueman
rm 'themes/hueman'

$ git rm -r --cached themes/hueman

$ git submodule add ./themes/hueman
A git directory for 'hueman' is found locally with remote(s):
  origin        git@github.com:shenlin9/shenlin.ltd/themes/hueman
If you want to reuse this local git directory instead of cloning again from
  git@github.com:shenlin9/shenlin.ltd/themes/hueman
use the '--force' option. If the local git directory is not the correct repo
or you are unsure what this means choose another name with the '--name' option.


$ git submodule add git@github.com:ppoffice/hexo-theme-hueman.git themes/hueman
Adding existing repo at 'themes/hueman' to the index


$ git ls-files -s|grep hueman
160000 c88e232969ad224a1869d764b403b8ca763484c9 0       themes/hueman

$ git submodule
 c88e232969ad224a1869d764b403b8ca763484c9 themes/hueman (v0.3.0-21-gc88e232)
fatal: no submodule mapping found in .gitmodules for path 'themes/next'

$ cat .gitmodules
[submodule "themes/hueman"]
        path = themes/hueman
        url = git@github.com:ppoffice/hexo-theme-hueman.git
```