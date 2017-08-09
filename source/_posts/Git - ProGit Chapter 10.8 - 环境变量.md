title: chapter 10.8 - Git 环境变量
categories:
  - Git
  - Book-ProGit
tags:
  - Git

---

Git 总是在一个 shell 中运行，并根据 shell 环境变量来调整运行方式。

<!--more-->

## 全局行为

`GIT_EXEC_PATH`

决定 Git 到哪找它的子程序，像 git-commit, git-diff 等。

可以用 `git --exec-path` 来查看当前设置.

`HOME`

HOME 变量是 Git 查找全局配置文件的地方，通常不会修改，太多其它东西都依赖它。

如果需要一个包括全局配置的便携版 Git， 可以在便携版 Git 的 shell 配置中覆盖 HOME 设置。

`PREFIX`

PREFIX 是用于查找系统级别配置的地方，通常也不会修改。

例如 Git 在 `$PREFIX/etc/` 查找 `gitconfig` 此文件.

`GIT_CONFIG_NOSYSTEM`

表示禁用系统级别的配置文件，当系统配置影响了你的命令，而你又无权限修改的时候很有用。

`GIT_PAGER`

控制在命令行上显示多页输出的程序。 没有设置则使用 PAGER .

`GIT_EDITOR`

当用户需要编辑一些文本（比如提交信息）时， Git 会启动这个编辑器。 没有设置则用 EDITOR 。


## 版本库位置

`GIT_DIR` 

是 .git 目录的位置. 如果没有设置， Git 会按照目录树逐层向上查找 .git 目录，直到到达 ~ 或 /。

`GIT_CEILING_DIRECTORIES`

控制查找 .git 目录的行为。 如果访问加载很慢的目录（如那些磁带机上的或通过网络连接访问的），你可能会想让 Git 早点停止尝试，尤其是 shell 构建时调用了 Git 。

`GIT_WORK_TREE`

是非空版本库的工作目录的根路径 如果没指定，就使用 $GIT_DIR 的父目录。

`GIT_INDEX_FILE`

是索引文件的路径（只有非空版本库有）

`GIT_OBJECT_DIRECTORY`

用来指定 .git/objects 目录的位置。

`GIT_ALTERNATE_OBJECT_DIRECTORIES`

一个冒号分割的列表 (格式类似 /dir/one:/dir/two:…) 用来告诉 Git 到哪里去找不在 `GIT_OBJECT_DIRECTORY` 目录中的对象. 

如果很多项目有相同内容的大文件，这个可以用来避免存储过多备份。


## 路径规则

所谓 “pathspec” 是指在 Git 中如何指定路径， 包括通配符的使用。

路径和通配符会在 `.gitignore` 文件中用到，命令行里也会用到，如 `git add *.c`。

`GIT_GLOB_PATHSPECS`

设置为 1, 通配符在路径规则中默认表现为通配符（这是默认设置）; 

`GIT_NOGLOB_PATHSPECS`

设置为 1,通配符在路径规则中默认仅匹配字面。意思是 `*.c` 只会匹配 文件名是 `*.c` 的文件， 而不是以 `.c` 结尾的文件。

可以在各个路径规格中用 `:(glob)` 或 `:(literal)` 开头来覆盖这个配置，如 `:(glob)*.c` 。

`GIT_LITERAL_PATHSPECS`

禁用上面的两种行为；通配符将不能用，前缀覆盖也不能用。

`GIT_ICASE_PATHSPECS`

让所有的路径规格忽略大小写。


## 提交

Git 提交对象的创建通常最后是由 git-commit-tree 来完成， git-commit-tree 用这些环境变量作主要的信息源。 仅当这些值不存在才回退到预置的值。

`GIT_AUTHOR_NAME`

是 “author” 字段的可读的名字。

`GIT_AUTHOR_EMAIL`

是 “author” 字段的邮件。

`GIT_AUTHOR_DATE`

是 “author” 字段的时间戳。

`GIT_COMMITTER_NAME`

是 “committer” 字段的可读的名字。

`GIT_COMMITTER_EMAIL`

是 “committer” 字段的邮件。

`GIT_COMMITTER_DATE`

是 “committer” 字段的时间戳。

`EMAIL`

如果 user.email 没有配置， 就会用到 `EMAIL` 指定的邮件地址。 如果 这个 也没有设置， Git 继续回退使用系统用户和主机名。


## 网络

`GIT_CURL_VERBOSE`

Git 使用 curl 库通过 HTTP来完成网络操作， 所以 `GIT_CURL_VERBOSE` 告诉 Git 显示所有由那个库产生的消息。 这跟在命令行执行 `curl -v` 差不多。

`GIT_SSL_NO_VERIFY`

告诉 Git 不用验证 SSL 证书。 这在有些时候是需要的， 例如你用一个自己签名的证书通过 HTTPS 来提供 Git 服务， 或者你正在搭建 Git 服务器，还没有安装完全的证书。

`GIT_HTTP_LOW_SPEED_LIMIT`,`GIT_HTTP_LOW_SPEED_TIME`

如果 Git 操作在网速低于 `GIT_HTTP_LOW_SPEED_LIMIT` 字节／秒，并且持续 `GIT_HTTP_LOW_SPEED_TIME` 秒以上的时间，Git 会终止那个操作。

这些值会覆盖 `http.lowSpeedLimit` 和 `http.lowSpeedTime` 配置的值。

`GIT_HTTP_USER_AGENT`

设置 Git 在通过 HTTP 通讯时用到的 user-agent。 默认值类似于 git/2.0.0 。


## 比较和合并

`GIT_DIFF_OPTS`

这个有点起错名字了 有效值仅支持 `-u<n>` 或 `--unified=<n>`，用来控制在 git diff 命令中显示的内容行数。

`GIT_EXTERNAL_DIFF`

用来覆盖 `diff.external` 配置的值。 如果设置了这个值， 当执行 `git diff` 时，Git 会调用该程序。

`GIT_DIFF_PATH_COUNTER`

对于 `GIT_EXTERNAL_DIFF` 或 `diff.external` 指定的程序有用。 前者表示在一系列文件中哪个是被比较的（从 1 开始）

`GIT_DIFF_PATH_TOTAL`

对于 `GIT_EXTERNAL_DIFF` 或 `diff.external` 指定的程序有用。 表示每批文件的总数。

`GIT_MERGE_VERBOSITY`

控制递归合并策略的输出。 允许的值有下面这些：
0 什么都不输出，除了可能会有一个错误信息。
1 只显示冲突。
2 还显示文件改变。
3 显示因为没有改变被跳过的文件。
4 显示处理的所有路径。
5 显示详细的调试信息。
默认值是 2.


## 调试

 Git 内置了相当完整的跟踪信息，你需要做的就是把它们打开。 这些变量的可以用的值如下：

* “true”, “1”, 或 “2” – 跟踪类别写到标准错误输出.

* 以 / 开头的绝对路径 – 跟踪输出会被写到那个文件。


`GIT_TRACE`
控制常规跟踪，它并不适用于特殊情况。 它跟踪的范围包括别名的展开和其他子程序的委托。
```
$ GIT_TRACE=true git lga
```

`GIT_TRACE_PACK_ACCESS`
控制访问打包文件的跟踪信息 第一个字段是被访问的打包文件，第二个是文件的偏移量：
```
$ GIT_TRACE_PACK_ACCESS=true git status
```

`GIT_TRACE_PACKET`
打开网络操作包级别的跟踪信息
```
$ GIT_TRACE_PACKET=true git ls-remote origin
```

`GIT_TRACE_PERFORMANCE`
控制性能数据的日志打印。 输出显示了每个 Git 命令调用花费的时间。
```
$ GIT_TRACE_PERFORMANCE=true git gc
```

`GIT_TRACE_SETUP`
显示 Git 发现的关于版本库和交互环境的信息
```
$ GIT_TRACE_SETUP=true git status
```

## 其他

`GIT_SSH`

如果指定了 `GIT_SSH`， Git 连接 SSH 主机时会用指定的程序代替 ssh 。 它会被用 `$GIT_SSH [username@]host [-p <port>] <command>` 的命令方式调用。

这不是配置定制 ssh 调用方式的最简单的方法; 它不支持额外的命令行参数， 所以你必须写一个封装脚本然后让 GIT_SSH 指向它。 可能用 ~/.ssh/config 会更简单。

`GIT_ASKPASS`

覆盖了 `core.askpass` 配置。 这是 Git 需要向用户请求验证时用到的程序，它接受一个文本提示作为命令行参数，并在 stdout 中返回应答。 (查看 凭证存储 访问更多相关内容)

`GIT_NAMESPACE`

控制有命令空间的引用的访问，与 `--namespace` 标志是相同的. 

这主要在服务器端有用，如果你想在一个版本库中存储单个版本库的多个 fork, 只要保持引用是隔离的就可以。

`GIT_FLUSH`

强制 Git 在向标准输出增量写入时使用没有缓存的 I/O。

设置为 1 让 Git 刷新更多， 设置为 0 则使所有的输出被缓存。

默认值（若此变量未设置）是根据活动和输出模式的不同选择合适的缓存方案。

`GIT_REFLOG_ACTION`

可以指定描述性的文字写到 reflog 中，例子：

```
$ GIT_REFLOG_ACTION="my action" git commit --allow-empty -m 'my message'
[master 9e3d55a] my message
$ git reflog -1
9e3d55a HEAD@{0}: my action: my message
```

