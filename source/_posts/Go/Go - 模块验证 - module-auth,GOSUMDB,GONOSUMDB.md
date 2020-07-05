---
title: Go - 模块验证 - module-auth
categories:
- Go
tags:
- Go
- module-auth
- GOSUMDB
- GONOSUMDB
date: 2020-06-18 15:16:32
---

Go - 模块验证 - module-auth

<!--more-->

## 模块验证

go  命令会验证每个下载的模块，检查今天为特定的模块版本下载的字节是否与昨天下载的
字节匹配(???)。这样可以确保构建是可重复的，并检测是否引入了意想不到的更改（无论
是否恶意）。

在每个模块的根目录下，`go.mod` 文件的旁边，go 命令还维护着一个名为 `go.sum` 的文
件，其内容是此模块的依赖项的加密校验和。

`go.sum` 文件中每行的形式一致，都是有 3 部分组成：

	<module> <version>[/go.mod] <hash>

每个模块的每个版本都会在 `go.sum` 文件中存储两行：
* 第一行是此模块此版本的目录树哈希值
* 第二行在版本号后面添加 `/go.mod`，后面是此模块此版本的 `go.mod` 文件(可能是合
    成的)的哈希值

例如：
```
github.com/davecgh/go-spew v1.1.0 h1:ZDRjVQ15GmhC3fiQ8ni8+OwkZQO4DARzQgrnXU1Liz8=
github.com/davecgh/go-spew v1.1.0/go.mod h1:J7Y8YcW2NihsgmVo/mv3lAwl/skON4iLHjSsI+c5H38=
github.com/pmezard/go-difflib v1.0.0 h1:4DBwDE0NGyQoBHbLQYPwSUPoCMWR5BEzIk/f1lZbAQM=
github.com/pmezard/go-difflib v1.0.0/go.mod h1:iKH77koFhYxTK1pcRnkKkqfTogsbg7gZNVY4sRDYZ/4=
github.com/spf13/pflag v1.0.5 h1:iy+VFUOCP1a+8yFto/drg2CJ5u0yRoB7fZw3DKv/JXA=
github.com/spf13/pflag v1.0.5/go.mod h1:McXfInJRrz4CZXVZOBLb0bTZqETkiAhM9Iw0y3An2Bg=
```

??? 为什么有的模块只有一行，如
```
github.com/stretchr/objx v0.1.0/go.mod h1:HFkY916IF+rwdDfMAkV7OtwuqBVzrE8GR6GFx+wExME=
gopkg.in/check.v1 v0.0.0-20161208181325-20d25e280405/go.mod h1:Co6ibVJAznAaIkqp8huTwlJQCZ016jof/cbN4VW5Yz0=
```

其中 `go.mod` 的哈希值允许下载和验证模块对应版本的 `go.mod` 文件，而 `go.mod` 文
件使得无需下载依赖模块的源代码，就可以计算依赖关系图。

哈希值以算法前缀 `h<N>:` 开头，目前定义的算法前缀只有 `h1:`，表示使用 SHA-256 算
法。

## Module authentication failures

go 命令维护着一份已下载软件包的缓存，并且在下载时，go 命令会计算并记录每个包的加
密校验和。在日常操作中，go 命令会把主模块的 `go.sum` 文件和 这些预先计算的校验和
进行比对，而不是在每次命令调用时重新计算校验和。

`go mod verify` 命令就是用于检查缓存的模块的校验和是否仍然同时匹配下载时记录的校
验和和 `go.sum` 中的对应的校验和条目。

在日常开发中，某个模块某个版本的校验和应该永远不会改变。主模块每次使用依赖项时，
go 命令都会把本地缓存的依赖模块(无论是否是刚下载)的校验和和主模块 `go.sum` 文件
中的相应校验和对比。如果校验和不匹配，则 go 命令将此不匹配报告为安全错误，并拒绝
构建。发生这种情况时，请谨慎操作：意外更改代码意味着今天的构建版本与昨天的版本不
匹配，并且意外的更改未必有好处。

如果 go 命令报告了 `go.sum` 中的一个不匹配，则说明下载缓存中的模块代码与主模块先
前版本中使用的代码不匹配。这时重要的是找出正确的校验和，确定是 `go.sum` 错误了还
是缓存中的代码是错误的。通常 `go.sum` 是正确的：你肯定是想使用和昨天相同的代码。

如果一个已下载模块是公共可用的模块，但 `go.sum` 中尚未包含，则 go 命令将查询 Go
校验和数据库以获取预期的 `go.sum` 行。如果模块代码与这些行不匹配，则 go 命令将报
告不匹配并退出。请注意，`go.sum` 中已列出的模块版本不会查询此校验和数据库。

如果 go 命令报告了 `go.sum` 中的不匹配，则需要弄清楚为什么今天下载的代码与昨天下
载的代码不同。

## GOSUMDB

环境变量 `GOSUMDB` 标识要使用的校验和数据库的名称以及其公钥和 URL，其中公钥和
URL 是可选的，如下所示：

	GOSUMDB="sum.golang.org"
	GOSUMDB="sum.golang.org+<publickey>"
	GOSUMDB="sum.golang.org+<publickey> https://sum.golang.org"

go 命令保存了 `sum.golang.org` 的公钥，域名 `sum.golang.google.cn` (在中国大陆可
用)也连接到 `sum.golang.org` 的校验和数据库。

使用任何其他的校验和数据库需要明确的给出其公钥。

URL 默认为 `https://` 后面跟着校验和数据库名。

`GOSUMDB` 默认值是 `"sum.golang.org"`, 是由 Google 运行的校验和数据库， 参考
https://sum.golang.org/

如果 `GOSUMDB` 被设置为 `"off"`，或者 `go get` 调用时使用了 `-insecure` 标记，则
不查询校验和数据库，则所有无法识别的模块也将被接受，但代价是放弃了所有模块经过验
证的可重复下载的安全保证。

针对特定的模块，有更好的方法来绕过校验和数据库，就是使用 `GOPRIVATE` 和
`GONOSUMDB` 环境变量。See 'go help module-private' for details.

命令 `go env -w` (see 'go help env') 可以设置这些变量。

## module-private

go 命令默认从公共模块镜像站点 `proxy.golang.org` 下载模块，默认使用
`sum.golang.org` 上的校验和数据库验证下载的公共 Go 模块，对于公共模块这些默认设
置工作的很好。

环境变量 `GOPRIVATE` 用于设置 go 命令把哪些儿模块视为是私有的(不是公开可用的)，
并因此不使用模块代理和校验和数据库。

`GOPRIVATE` 的格式是使用逗号分隔的列表，列表每一项是模块路径前缀的匹配模式(使用
Go 的 path.Match 语法)。

例如：

        GOPRIVATE=*.corp.example.com,rsc.io/private

上面的环境变量设置使得 go 命令把路径前缀与上面任一模式匹配的任何模块视为私有模块
，包括 `git.corp.example.com/xyzzy`, `rsc.io/private`, `rsc.io/private/quux`.

`GOPRIVATE` 环境变量也可以由其他工具使用以标识非公共模块。例如，编辑器可以使用
`GOPRIVATE` 来决定是否超链接导入的软件包到 `godoc.org` 页面。

For fine-grained control over module download and validation, the GONOPROXY and GONOSUMDB environment variables accept the same kind of glob list and override GOPRIVATE for the specific decision of whether to use the proxy and checksum database, respectively.
为了对模块的下载和验证进行细粒度的控制，`GONOPROXY` 和 `GONOSUMDB` 环境变量接受相同类型的 glob 列表，并覆盖 `GOPRIVATE` 以分别决定是否使用代理和校验和数据库。

For example, if a company ran a module proxy serving private modules, users would configure go using:

        GOPRIVATE=*.corp.example.com
        GOPROXY=proxy.example.com
        GONOPROXY=none

This would tell the go command and other tools that modules beginning with a corp.example.com subdomain are private but that the company proxy should be used for downloading both public and private modules, because GONOPROXY has been set to a pattern that won't match any modules, overriding GOPRIVATE.

The 'go env -w' command (see 'go help env') can be used to set these variables for future go command invocations.
