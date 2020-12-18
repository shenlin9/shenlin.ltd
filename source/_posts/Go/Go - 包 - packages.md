---
title: Go - 包 - packages
categories:
- Go
tags:
- Go
- packages
date: 2020-08-19 07:48:47
---

Go - 包 - packages

<!--more-->

## packages

许多 Go 命令适用于一组软件包，形式为：

        go action [packages]

通常 `[packages]` 是一个导入路径的列表。

导入路径用来指示软件包所在的目录，可以是：
* 以 `/` 开头的绝对路径
* 以 `.`、`..` 开头的相对路径
* 以包名开头，如 P，则会去 GOPATH 环境变量的保存的目录列表里查找 P，如 `DIR1/src/P`， `DIR2/src/P`
* 省略，则默认为当前目录，命令操作将针对当前目录的软件包。

使用 go 工具构建包时，有 4 个保留名不可用于路径名：
* main，表示独立可执行文件中的顶层程序包
* all，表示找到 GOPATH 环境变量保存的所有目录中的所有软件包。 例如，`go list all` 列出了本地系统上的所有软件包。 使用模块时，`all` 表示找到主模块及其依赖模块的所有软件包，甚至也包括了进行测试所需依赖的软件包。
- "all" expands to all packages found in all the GOPATH trees. For example, 'go list all' lists all the packages on the local system. When using modules, "all" expands to all packages in the main module and their dependencies, including dependencies needed by tests of any of those.
* std，像 all 一样，但只扩展为标准 Go 库中的软件包。
- "std" is like all but expands to just the packages in the standard Go library.
* cmd，扩展为 Go 存储库的命令及其内部库。
- "cmd" expands to the Go repository's commands and their internal libraries.

Import paths beginning with "cmd/" only match source code in the Go repository.
以“ cmd /”开头的导入路径仅与Go存储库中的源代码匹配。

如果导入路径包含一个或多个 `...` 通配符，则表示此导入路径是个模式。`...` 通配符
An import path is a pattern if it includes one or more "..." wildcards, each of which can match any string, including the empty string and strings containing slashes. Such a pattern expands to all package directories found in the GOPATH trees with names matching the patterns.

To make common patterns more convenient, there are two special cases.
First, /... at the end of the pattern can match an empty string, so that net/... matches both net and packages in its subdirectories, like net/http.
Second, any slash-separated pattern element containing a wildcard never participates in a match of the "vendor" element in the path of a vendored package, so that ./... does not match packages in subdirectories of ./vendor or ./mycode/vendor, but ./vendor/... and ./mycode/vendor/... do.

Note, however, that a directory named vendor that itself contains code is not a vendored package: cmd/vendor would be a command named vendor, and the pattern cmd/... matches it.  See golang.org/s/go15vendor for more about vendoring.

An import path can also name a package to be downloaded from a remote repository. Run 'go help importpath' for details.

Every package in a program must have a unique import path.  By convention, this is arranged by starting each path with a unique prefix that belongs to you. For example, paths used internally at Google all begin with 'google', and paths denoting remote repositories begin with the path to the code, such as 'github.com/user/repo'.

Packages in a program need not have unique package names, but there are two reserved package names with special meaning.  The name main indicates a command, not a library.  Commands are built into binaries and cannot be imported.  The name documentation indicates documentation for a non-Go program in the directory. Files in package documentation are ignored by the go command.

As a special case, if the package list is a list of .go files from a single directory, the command is applied to a single synthesized package made up of exactly those files, ignoring any build constraints in those files and ignoring any other files in the directory.

Directory and file names that begin with "." or "_" are ignored by the go tool, as are directories named "testdata".
