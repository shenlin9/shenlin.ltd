---
title: Go - Module 模块 - GOPATH,GOROOT,GOBIN,go.mod
categories:
- Go
tags:
- Go
- module
date: 2020-03-01 16:49:07
---

Go - Module 模块 - GOPATH,GOROOT,GOBIN,go.mod

<!--more-->

显示模块说明：
```
go help modules
```

Go 以前使用 GOPATH 来指定给定的版本需要哪些源文件，后来被模块代替。

Go 模块是一些相关包 (package) 的集合，模块也是交换源代码的基本单位。

所有的 go 命令都内置了对模块的支持，可以记录和解决对其他模块的依赖。

## GOPATH

Go 路径用于解析 import 语句，由 `go/build` 包实现。

`GOPATH` 环境变量列出了查找 Go 代码的位置：
* Unix 系统中，该值是冒号分隔的字符串
* Windows 系统中，该值是分号分隔的字符串

如果未设置环境变量 `GOPATH`，则其默认值为用户主目录中名为 `go` 的子目录(除非此目
录中包含 Go 发行版)：
* Unix 系统中为 `$HOME/go`
* Windows 系统中为 `%USERPROFILE%\go`

使用命令 `go env GOPATH` 可以查看当前 GOPATH 的值。

GOPATH 中列出的每个目录，必须具有规定的结构：
* src
* pkg
* bin

**src**

src 目录包含源代码src 下的路径确定了导入路径或可执行文件名称。

**pkg**

pkg 目录包含已安装的包对象，在 Go 的目录树中，每个操作系统和架构的组合对都有自己
的 pkg 子目录，即 `pkg/GOOS_GOARCH`

如果 GOPATH 的目录列表中有一个 DIR 目录，DIR 目录下有一个源代码目录
`DIR/src/foo/bar`，则其导入路径为 `"foo/bar"`，其编译后的包被安装到了
`"DIR/pkg/GOOS_GOARCH/foo/bar.a"`

**bin**

bin 目录包含已编译的命令。

每个已编译的命令都使用它的源代码目录来命名，但不是使用整个完整路径，而是最后一级
目录名。例如：

    源代码目录为            `DIR/src/foo/quux`，
    则编译后的命令被安装到  `DIR/bin/quux`，
    而不是                  `DIR/bin/foo/quux`

前缀 `foo/` 被去除，这样可以通过把 `DIR/bin` 目录添加到系统环境变量 `PATH` 中来
获取已安装的命令。

如果设置了环境变量 GOBIN，则编译后的命令会被安装到 GOBIN 指定的目录，而不是 `DIR
/bin` 目录。

GOBIN 指定的目录必须是绝对路径。

下面是一个目录结构的例子：

    GOPATH=/home/user/go

    /home/user/go/
        src/
            foo/
                bar/               (go code in package bar)
                    x.go
                quux/              (go code in package main)
                    y.go
        bin/
            quux                   (installed command)
        pkg/
            linux_amd64/
                foo/
                    bar.a          (installed package object)

Go 会搜索 GOPATH 列表中的每个目录来查找源代码，但是新包总是下载保存到列表中的第
一个目录。

See https://golang.org/doc/code.html for an example.

## GOPATH and Modules

当使用模块时，GOPATH 就不再被用于解决导入的路径问题。

但是 GOPATH 仍然被用来存储下载的源代码(位于 `GOPATH/pkg/mod` 目录) 和编译后的命
令(位于 `GOPATH/bin`)

## Internal Directories

Go 的目录结构中一个特殊的目录是 `internal`，位于 `internal` 和其子目录中的代码，
只有 `internal` 目录的父目下的代码才可以调用，即只有以 `internal` 目录的父目为根
的代码才可以调用 `internal`。

这是上面目录结构的扩展版本：

    /home/user/go/
        src/
            crash/
                bang/              (go code in package bang)
                    b.go
            foo/                   (go code in package foo)
                f.go
                bar/               (go code in package bar)
                    x.go
                internal/
                    baz/           (go code in package baz)
                        z.go
                quux/              (go code in package main)
                    y.go

`z.go` 的导入路径为 `"foo/internal/baz"`，但是这行导入语句只能出现在 foo 的目录
树源代码中，即 `foo/f.go`， `foo/bar/x.go`， `foo/quux/y.go` 可以使用这条导入语
句，但 `crash/bang/b.go` 中不可以。

See https://golang.org/s/go14internal for details.

## Vendor Directories

Go 1.6 可以使用外部依赖项的本地副本(local copies of external dependencies)来满足
导入外部依赖项，通常称为供应商。

和 `internal` 类似，`vendor` 下的代码只允许位于 `vendor` 父目录的目录树中的代码
导入，但是导入路径和 `internal` 不同，`vendor` 下代码的导入路径省略前缀到 vendor
目录(vendor 目录本身也省略)，即可理解为 `vendor` 下代码的导入路径是相对于 `vendor`
目录的，而 `internal` 导入路径是相对于 `src` 目录的。

例子：

    /home/user/go/
        src/
            crash/
                bang/              (go code in package bang)
                    b.go
            foo/                   (go code in package foo)
                f.go
                bar/               (go code in package bar)
                    x.go
                vendor/
                    crash/
                        bang/      (go code in package bang)
                            b.go
                    baz/           (go code in package baz)
                        z.go
                quux/              (go code in package main)
                    y.go

和 internal 同样的可见性规则，但是 `z.go` 代码可被导入为 `"baz"`，即相对于
`vendor` 目录，而不是相对于 `src` 的 `"foo/vendor/baz"` 目录。

Code in vendor directories deeper in the source tree shadows code in higher directories. Within the subtree rooted at foo, an import of "crash/bang" resolves to "foo/vendor/crash/bang", not the top-level "crash/bang".

`vendor` 目录中的代码不再进行导入路径检查。(see 'go help importpath').

When 'go get' checks out or updates a git repository, it now also updates submodules.
使用 `go get` 命令检出或更新一个版本库时，现在也会更新子模块。

Vendor directories do not affect the placement of new repositories being checked out for the first time by 'go get': those are always placed in the main GOPATH, never in a vendor subtree.
Vendor 目录不影响 `go get` 首次检出的新代码库的位置，它们总是放置到 GOPATH 中，从
不会检出到 vendor 子目录。

See https://golang.org/s/go15vendor for details.

## 模块支持

### go.mod 文件

在当前目录或父目录中找到 go.mod 文件时，默认情况下会激活模块感知模式。

使用模块的最快方式，就是在代码目录下创建一个 `go.mod` 文件，然后在这个目录树下运
行相关的 go 命令。

### GO111MODULE

go 命令目前仍然支持临时环境变量：`GO111MODULE`，其取值为字符串，有 3 个：
* on
* off
* auto，此为默认值

**GO111MODULE=on**

此时 go 命令运行于模块感知模式，Go 代码需要使用模块，而不会考虑 GOPATH

**GO111MODULE=off**

此时 go 命令运行于 GOPATH 模式，go 命令不会启用模块支持，而是去供应商目录和
GOPATH 指定的目录寻找依赖。

**GO111MODULE=auto**

设置为 auto 或没设置值时，go 命令启用还是禁用模块支持取决于当前的运行目录：如果
当前的目录包含 `go.mod` 文件，或当前的目录是包含 `go.mod` 文件目录的子目录，则启
用模块支持。

### 注意

在模块感知模式下，GOPATH 不再对编译期间的 import 语句导入依赖路径起作用，但是仍
然在 `GOPATH/pkg/mod` 目录下存储了下载的依赖，仍然会安装命令到 `GOPATH/bin` 目录
下(除非设置了 GOBIN)

## 定义模块

在 Go 源文件的根目录下放一个 `go.mod` 文件，这个 `go.mod` 文件再加上 Go 源文件的
目录树就定义了一个 Go 模块。

包含 `go.mod` 文件的目录称为模块的根目录，通常模块根目录也对应一个源代码根，但也
不是必需。

Go 模块是模块根目录下所有 Go 包和其子目录下所有 Go 包的集合，但注意不包括那些有自
己的 `go.mod` 文件的子目录树。

模块的路径是导入模块时的路径前缀，是相对于模块根目录的。

`go.mod` 文件定义了当前模块的路径，并通过模块路径和版本号的方式列出了需要导入的其
他模块和其版本号。

例如，下面的 `go.mod` 文件声明了包含它的目录是模块的根目录，模块路径是
`example.com/m`，并且声明了依赖的特定版本的两个模块：

	module example.com/m

	require (
		golang.org/x/text v0.3.0
		gopkg.in/yaml.v2 v2.1.0
	)

`go.mod` 文件还可以指定替换和排除的模块版本号，但是只在直接编译此模块时起作用，
若此模块被纳入一个更大的编译时，则这些设置会被忽略。

最简单的模块，在模块的根目录下创建一个 `go.mod` 文件，然后写入一条 `module` 语句
，例如：`module example.com/m`，也可以使用命令完成上述过程：
```
go mod init example.com/m
```

> 如果一个工程中使用了依赖管理工具，如 godep, glide, dep， 则 `go mod init` 命令
> 会根据已存在的设置自动添加相关的 require 语句。

一旦 `go.mod` 文件建立，则无需其他额外步骤，go 命令像 `go build`, `go test`, `go
list` 会自动添加需要的新依赖来满足导入操作。

## The main module and the build list

一个程序项目可以包括多个模块，其中一个模块必须是主模块 main module，作为程序的执
行入口，例如若 go 去 x 目录下执行 `go run ...` 之类的命令来启动程序，则包含了 x 
目录的模块就是主模块，go 命令通过从当前目录逐级向上查找 `go.mod` 文件来找到主模
块的根目录。

主模块的 `go.mod` 文件可以通过 `require`， `replace`， `exclude` 语句精确的定义
可供 go 命令使用的包的集合，而 `require` 语句定义的依赖模块也有自己的 `go.mod` 
文件，但这些依赖模块这时不是作为独立模块，而是作为主模块的子模块，这时依赖模块中
定义的 `replace` 和 `exclude` 语句都会被忽略，而只考虑 `require` 语句，这种处理
方式可以让主模块完全控制其自身的构建，而不是还要受依赖模块的控制。

提供了构建程序时所需包的模块集合，称为构建列表 build list。构建列表最开始只包含
主模块，然后 go 命令把主模块中要求的依赖模块版本添加到构建列表，然后再去递归的找
这些依赖模块的依赖模块，直至所有依赖模块添加完成。

如果一个模块有多个版本被添加到构建列表，则只保留最新的版本(根据语义版本排序)。

`go list` 命令提供了主模块和构建列表的信息：

	go list -m              # print path of main module
	go list -m -f={{.Dir}}  # print root directory of main module
	go list -m all          # print build list

## Maintaining module requirements

`go.mod` 文件既可以被程序员读取和更改，也可以被命令程序读取和更改，go 命令会自动
的更新 `go.mod` 文件，以维持 `go.mod` 文件的标准格式和 `require` 语句的准确性。

任何 go 命令发现了不熟悉的 import 语句，都会查找包含这条 import 语句的模块，并把
此模块的最新版本自动添加到 `go.mod` 文件，因此，根据上述描述的规则，在大多数情况
下，只需要在源代码中添加 import 语句，然后运行 `go build`, `go test`, 甚至是
`go list` 即可，因为 go 命令会分析软件包，发现不熟悉的 import 语句并解决依赖，最
后自动更新到`go.mod` 文件。

当 go 命令添加依赖模块时很容易，因为 go 命令只需要检测到一条 import 语句导入了一
个包，然后 go 命令就可以把包含这个包的模块添加到构建列表，然而，从构建列表移除一
个模块却不像添加这么容易，因为要确定此模块真的不再被依赖，则需要确定此模块中包含
的每一个包都不再被依赖，就需要一个此模块的全局视图，包括了所有可能的构建配置(架
构、操作系统、构建标签等)。命令 `go mod tidy` 就可以构建这样的全局视图，然后基于
此视图添加缺少的模块，移除不需要的模块。

go 命令在维护 `go.mod` 中的 `require` 语句时，会跟踪哪些依赖模块提供的包是被当前
模块直接导入的，哪些依赖模块提供的包只是被通过二三层级的依赖模块间接使用的，后者
在 `go.mod` 文件中会被注释标明：`// indirect`，且一旦直接导入的依赖包隐含了这些
间接导入的依赖包，则间接导入的依赖包会自动的被从 `go.mod` 文件中移除。

只有在下列两种情况时，间接依赖的模块才会出现在 `go.mod` 文件中：
* 当使用的模块无法说明其自身的依赖关系
* 当把一个模块的依赖显式的升级后超过了它声明的需求

由于上述的自动维护规则，`go.mod` 文件中的信息是当前构建的最新的可读性描述。

`go get` 命令更新 `go.mod` 文件以更改构建中使用的模块版本。一个模块的升级可能意
味着也要升级其他模块，同样，一个模块的降级可能意味着也要降级其他模块，`go get`
命令也会进行这些隐式的升降级。

如果直接编辑 `go.mod` 文件，类似 `go build` 和 `go list` 这样的命令会假定要进行
升级，并自动的进行任何隐式的升级，同时同步修改 `go.mod` 中的版本号。

### build flags

go 命令调用时可以使用一些构建标记(build flags)，例如 `-mod` 就是标记之一，此标
记对更新和使用 `go.mod` 文件提供了更多控制，其取值有 3 个：
* readonly
* vendor
* mod

注意：`go mod` 命令不采用任何构建标记。

**-mod=readonly**

如果 go 命令调用时使用 `-mod=readonly`，

则不允许 go 命令对 `go.mod` 文件进行上述规则的隐式更新(有个例外，就是 `go get` 
命令不受影响仍然可以更新 `go.mod` 文件)，此项设置用于核实 `go.mod` 文件是否需要
更新最有用，例如在连续集成和测试系统时。

**-mod=vendor**

如果 go 命令调用时使用 `-mod=vendor`，

则 go 命令将直接从主模块的供应商目录加载包，而不是把模块下载到模块缓存，再从模块
缓存中加载包。

且 go 命令假定供应商目录包含了正确的依赖模块，不再从 `go.mod` 文件中计算依赖模块
的版本号。

但是 go 命令会检查 `vendor/modules.txt` 文件(`go mod vendor` 命令生成)中是否包含
和 `go.mod` 中一致的的元数据。

**-mod=mod**

如果 go 命令调用时使用 `-mod=mod`，

即使有供应商目录，go 命令也从模块缓存中加载模块。

**不使用 -mod 标记**

同时满足下列 3 个条件，则 go 命令的行为和使用 `-mod=vendor` 标记时一致：
* `go.mod` 中 go 的版本大于等于 1.14
* 调用 go 命令时没有使用 `-mod` 标记
* 有供应商目录

简单说，就是新版本的 Go 默认使用供应商目录。

## Pseudo-versions

The go.mod file and the go command more generally use semantic versions as
the standard form for describing module versions, so that versions can be
compared to determine which should be considered earlier or later than another.
A module version like v1.2.3 is introduced by tagging a revision in the
underlying source repository. Untagged revisions can be referred to
using a "pseudo-version" like v0.0.0-yyyymmddhhmmss-abcdefabcdef,
where the time is the commit time in UTC and the final suffix is the prefix
of the commit hash. The time portion ensures that two pseudo-versions can
be compared to determine which happened later, the commit hash identifes
the underlying commit, and the prefix (v0.0.0- in this example) is derived from
the most recent tagged version in the commit graph before this commit.

There are three pseudo-version forms:

vX.0.0-yyyymmddhhmmss-abcdefabcdef is used when there is no earlier
versioned commit with an appropriate major version before the target commit.
(This was originally the only form, so some older go.mod files use this form
even for commits that do follow tags.)

vX.Y.Z-pre.0.yyyymmddhhmmss-abcdefabcdef is used when the most
recent versioned commit before the target commit is vX.Y.Z-pre.

vX.Y.(Z+1)-0.yyyymmddhhmmss-abcdefabcdef is used when the most
recent versioned commit before the target commit is vX.Y.Z.

Pseudo-versions never need to be typed by hand: the go command will accept
the plain commit hash and translate it into a pseudo-version (or a tagged
version if available) automatically. This conversion is an example of a
module query.

## Module queries

The go command accepts a "module query" in place of a module version
both on the command line and in the main module's go.mod file.
(After evaluating a query found in the main module's go.mod file,
the go command updates the file to replace the query with its result.)

A fully-specified semantic version, such as "v1.2.3",
evaluates to that specific version.

A semantic version prefix, such as "v1" or "v1.2",
evaluates to the latest available tagged version with that prefix.

A semantic version comparison, such as "<v1.2.3" or ">=v1.5.6",
evaluates to the available tagged version nearest to the comparison target
(the latest version for < and <=, the earliest version for > and >=).

The string "latest" matches the latest available tagged version,
or else the underlying source repository's latest untagged revision.

The string "upgrade" is like "latest", but if the module is
currently required at a later version than the version "latest"
would select (for example, a newer pre-release version), "upgrade"
will select the later version instead.

The string "patch" matches the latest available tagged version
of a module with the same major and minor version numbers as the
currently required version. If no version is currently required,
"patch" is equivalent to "latest".

A revision identifier for the underlying source repository, such as
a commit hash prefix, revision tag, or branch name, selects that
specific code revision. If the revision is also tagged with a
semantic version, the query evaluates to that semantic version.
Otherwise the query evaluates to a pseudo-version for the commit.
Note that branches and tags with names that are matched by other
query syntax cannot be selected this way. For example, the query
"v2" means the latest version starting with "v2", not the branch
named "v2".

All queries prefer release versions to pre-release versions.
For example, `"<v1.2.3"` will prefer to return "v1.2.2"
instead of "v1.2.3-pre1", even though "v1.2.3-pre1" is nearer
to the comparison target.

Module versions disallowed by exclude statements in the
main module's go.mod are considered unavailable and cannot
be returned by queries.

For example, these commands are all valid:

	go get github.com/gorilla/mux@latest    # same (@latest is default for 'go get')
	go get github.com/gorilla/mux@v1.6.2    # records v1.6.2
	go get github.com/gorilla/mux@e3702bed2 # records v1.6.2
	go get github.com/gorilla/mux@c856192   # records v0.0.0-20180517173623-c85619274f5d
	go get github.com/gorilla/mux@master    # records current meaning of master

## Module compatibility and semantic versioning

The go command requires that modules use semantic versions and expects that
the versions accurately describe compatibility: it assumes that v1.5.4 is a
backwards-compatible replacement for v1.5.3, v1.4.0, and even v1.0.0.
More generally the go command expects that packages follow the
"import compatibility rule", which says:

"If an old package and a new package have the same import path,
the new package must be backwards compatible with the old package."

Because the go command assumes the import compatibility rule,
a module definition can only set the minimum required version of one
of its dependencies: it cannot set a maximum or exclude selected versions.
Still, the import compatibility rule is not a guarantee: it may be that
v1.5.4 is buggy and not a backwards-compatible replacement for v1.5.3.
Because of this, the go command never updates from an older version
to a newer version of a module unasked.

In semantic versioning, changing the major version number indicates a lack
of backwards compatibility with earlier versions. To preserve import
compatibility, the go command requires that modules with major version v2
or later use a module path with that major version as the final element.
For example, version v2.0.0 of example.com/m must instead use module path
example.com/m/v2, and packages in that module would use that path as
their import path prefix, as in example.com/m/v2/sub/pkg. Including the
major version number in the module path and import paths in this way is
called "semantic import versioning". Pseudo-versions for modules with major
version v2 and later begin with that major version instead of v0, as in
v2.0.0-20180326061214-4fc5987536ef.

As a special case, module paths beginning with gopkg.in/ continue to use the
conventions established on that system: the major version is always present,
and it is preceded by a dot instead of a slash: gopkg.in/yaml.v1
and gopkg.in/yaml.v2, not gopkg.in/yaml and gopkg.in/yaml/v2.

The go command treats modules with different module paths as unrelated:
it makes no connection between example.com/m and example.com/m/v2.
Modules with different major versions can be used together in a build
and are kept separate by the fact that their packages use different
import paths.

In semantic versioning, major version v0 is for initial development,
indicating no expectations of stability or backwards compatibility.
Major version v0 does not appear in the module path, because those
versions are preparation for v1.0.0, and v1 does not appear in the
module path either.

Code written before the semantic import versioning convention
was introduced may use major versions v2 and later to describe
the same set of unversioned import paths as used in v0 and v1.
To accommodate such code, if a source code repository has a
v2.0.0 or later tag for a file tree with no go.mod, the version is
considered to be part of the v1 module's available versions
and is given an +incompatible suffix when converted to a module
version, as in v2.0.0+incompatible. The +incompatible tag is also
applied to pseudo-versions derived from such versions, as in
v2.0.1-0.yyyymmddhhmmss-abcdefabcdef+incompatible.

In general, having a dependency in the build list (as reported by 'go list -m all')
on a v0 version, pre-release version, pseudo-version, or +incompatible version
is an indication that problems are more likely when upgrading that
dependency, since there is no expectation of compatibility for those.

See https://research.swtch.com/vgo-import for more information about
semantic import versioning, and see https://semver.org/ for more about
semantic versioning.

## Module code layout

For now, see https://research.swtch.com/vgo-module for information
about how source code in version control systems is mapped to
module file trees.

## Module downloading and verification

The go command can fetch modules from a proxy or connect to source control
servers directly, according to the setting of the GOPROXY environment
variable (see 'go help env'). The default setting for GOPROXY is
"https://proxy.golang.org,direct", which means to try the
Go module mirror run by Google and fall back to a direct connection
if the proxy reports that it does not have the module (HTTP error 404 or 410).
See https://proxy.golang.org/privacy for the service's privacy policy.
If GOPROXY is set to the string "direct", downloads use a direct connection
to source control servers. Setting GOPROXY to "off" disallows downloading
modules from any source. Otherwise, GOPROXY is expected to be a comma-separated
list of the URLs of module proxies, in which case the go command will fetch
modules from those proxies. For each request, the go command tries each proxy
in sequence, only moving to the next if the current proxy returns a 404 or 410
HTTP response. The string "direct" may appear in the proxy list,
to cause a direct connection to be attempted at that point in the search.
Any proxies listed after "direct" are never consulted.

The GOPRIVATE and GONOPROXY environment variables allow bypassing the proxy for selected modules. See 'go help module-private' for details.

No matter the source of the modules, the go command checks downloads against known checksums, to detect unexpected changes in the content of any specific module version from one day to the next. This check first consults the current module's go.sum file but falls back to the Go checksum database, controlled by the GOSUMDB and GONOSUMDB environment variables. See 'go help module-auth' for details.

See 'go help goproxy' for details about the proxy protocol and also the format of the cached downloaded packages.

## Modules and vendoring

When using modules, the go command typically satisfies dependencies by
downloading modules from their sources and using those downloaded copies
(after verification, as described in the previous section). Vendoring may
be used to allow interoperation with older versions of Go, or to ensure
that all files used for a build are stored together in a single file tree.

The command 'go mod vendor' constructs a directory named vendor in the main
module's root directory that contains copies of all packages needed to support
builds and tests of packages in the main module. 'go mod vendor' also
creates the file vendor/modules.txt that contains metadata about vendored
packages and module versions. This file should be kept consistent with go.mod:
when vendoring is used, 'go mod vendor' should be run after go.mod is updated.

If the vendor directory is present in the main module's root directory, it will
be used automatically if the "go" version in the main module's go.mod file is
1.14 or higher. Build commands like 'go build' and 'go test' will load packages
from the vendor directory instead of accessing the network or the local module
cache. To explicitly enable vendoring, invoke the go command with the flag
-mod=vendor. To disable vendoring, use the flag -mod=mod.

Unlike vendoring in GOPATH, the go command ignores vendor directories in locations other than the main module's root directory.

## ??

默认 $GOPATH 路径下的模块支持是禁用的，所以要使用模块支持，把目录建立在 $GOPATH
路径之外。

`go.mod` 中的依赖是按模块为基本单位的，不可以模块中的包为基本单位

`go help mod`

`go help gopath`

## go.mod 文件

A module version is defined by a tree of source files, with a go.mod file in its root. When the go command is run, it looks in the current directory and then successive parent directories to find the go.mod marking the root of the main (current) module.
模块版本由根目录中带有 `go.mod` 文件的源文件树定义。

运行 go 命令时，它会在当前目录中查找，然后依次向上在每一级父目录中查找 `go.mod`，以标记主（当前）模块的根目录。

`go.mod` 文件：
* `go.mod` 文件本身是面向行的，每行只包含一个指令。
* 指令由一个动词加上后面跟着的参数组成。
* `go.mod` 文件使用 `//` 注释，不使用 "/* */" 注释。

例如：

	module my/thing
	go 1.12
	require other/thing v1.0.2
	require new/thing/v2 v2.3.4
	exclude old/thing v1.2.3
	replace bad/thing v1.4.5 => good/thing v1.4.5

这些动词包括：

	module      定义模块路径
	go          设置要求的 go 版本号
	require     设置需求的模块及其版本号或更高版本号
	exclude     设置排除的模块及其版本号
	replace     设置要把哪个版本的哪个模块替换为另一个模块的哪个版本号

    exclude 和 replace 仅应用于主模块的 `go.mod` 文件，在依赖模块里都被忽略。
    See https://research.swtch.com/vgo-mvs for details.

就像 Go 里的导入块语句一样，多个相同的动词也可以创建一个块：

	require (
		new/thing v2.3.4
		old/thing v1.2.3
	)

`go.mod` 文件被设计为既可以直接编辑，也可以轻松的通过程序更新，`go mod edit` 命
令就是用于解析和编辑 `go.mod` 文件的命令行接口，主要由程序和脚本调用。
See 'go help mod edit'.

The go command automatically updates go.mod each time it uses the module graph,
to make sure go.mod always accurately reflects reality and is properly
formatted. For example, consider this go.mod file:
每次 go 命令使用了模块关系图时，go 都会自动更新 `go.mod` 文件以确保 `go.mod` 准
确的反映了实际情况和文件内容被正确的进行了格式化。

        module M

        require (
                A v1
                B v1.0.0
                C v1.0.0
                D v1.2.3
                E dev
        )

        exclude D v1.2.3

go 命令自动更新 `go.mod` 文件时：
* 会将不规范的版本标识符重写为语义版本格式(semver)，因此上面的 A 的 v1 会变为
  v1.0.0，E 的 dev 会变为 dev 分支上最新提交的伪版本，也许是
  v0.0.0-20180523231146-b3f5c0f6e5f1。
* 会根据排除规则(exclude)修改需求规则(require)，如上面的 `exclude D v1.2.3` 语句
  会导致 `require` 部分使用 D 的下一个可用版本，可能是 `D v1.2.4` 或者 `D
  v1.3.0`。
* 会移除重复的需求，还会移除误导的需求(require)。例如，在上例中，如果 A v1.0.0 本
  身需要 B v1.2.0 和 C v1.0.0，则 `go.mod` 文件中对 B v1.0.0 的需求有误导，因为
  已被 A 对 B 的 v1.2.0 需求取代， `go.mod` 文件中对 C v1.0.0 的需求重复，因为 A
  已包含同样的版本需求，所以 B 和 C 都将被移除。但是如果模块 M 包含的包直接从 B
  或 C 导入了包，则对 B 或 C 的需求将保留，并且会更新为所使用的实际版本。
* 会重新规范格式化 `go.mod` 文件，这样将来的机械化更改(future mechanical
  changes)只会导致最小的变动。

因为模块图(module graph)定义了导入语句的含义，任何加载包的命令都会使用和更新
`go.mod` 文件，包括 `go build, go get, go install, go list, go test, go mod
graph, go mod tidy, go mod why`

The expected language version, set by the go directive, determines which
language features are available when compiling the module.  Language features
available in that version will be available for use.  Language features removed
in earlier versions, or added in later versions, will not be available. Note
that the language version does not affect build tags, which are determined by
the Go release being used.

go 指令设置的预期语言版本确定编译模块时哪些语言特性是可用的。该版本中可用的语言
功能将可用。在较早版本中删除或在较新版本中添加的语言功能将不可用。请注意，语言版
本不会影响构建标记(build tags)，该构建标记由所使用的 Go 发行版确定。

> go mod graph 命令以文本形式打印出模块需求图(已应用过了替换项)，输出的每行以空
> 格分隔两个字段：模块和此模块的依赖。
>
> 每个模块的描述格式为 `path@version` 的字符串，除了 main 模块没有 `@version` 后
> 缀。

> 例如：
> $ go mod graph
> github.com/poloxue/testmod golang.org/x/text@v0.3.2
> github.com/poloxue/testmod rsc.io/quote/v3@v3.1.0
> golang.org/x/text@v0.3.2 golang.org/x/tools@v0.0.0-20180917221912-90fa682c2a6e
> rsc.io/quote/v3@v3.1.0 rsc.io/sampler@v1.3.0
> rsc.io/sampler@v1.3.0 golang.org/x/text@v0.0.0-20170915032832-14c0d48ead0c

