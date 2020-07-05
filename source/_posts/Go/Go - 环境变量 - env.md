---
title: Go - 环境变量 - env
categories:
- Go
tags:
- Go
- env
date: 2020-06-18 15:23:37
---

Go - 环境变量 - env

<!--more-->

## go env

`go env` 输出环境变量信息。

用法: 
```
go env [-json] [-u] [-w] [var ...]
```

默认输出所有环境变量信息
```go
> go env
set GO111MODULE=auto
set GOARCH=amd64
set GOBIN=
set GOCACHE=C:\Users\T460P\AppData\Local\go-build
set GOENV=C:\Users\T460P\AppData\Roaming\go\env
set GOEXE=.exe
set GOFLAGS=
set GOHOSTARCH=amd64
set GOHOSTOS=windows
set GOINSECURE=
set GONOPROXY=
set GONOSUMDB=
set GOOS=windows
set GOPATH=C:\Users\T460P\go
set GOPRIVATE=
set GOPROXY=https://goproxy.io,direct
set GOROOT=c:\go
set GOSUMDB=sum.golang.org
set GOTMPDIR=
set GOTOOLDIR=c:\go\pkg\tool\windows_amd64
set GCCGO=gccgo
set AR=ar
set CC=gcc
set CXX=g++
set CGO_ENABLED=1
set GOMOD=
set CGO_CFLAGS=-g -O2
set CGO_CPPFLAGS=
set CGO_CXXFLAGS=-g -O2
set CGO_FFLAGS=-g -O2
set CGO_LDFLAGS=-g -O2
set PKG_CONFIG=pkg-config
set GOGCCFLAGS=-m64 -mthreads -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=Z:\TEMP\go-build322407970=/tmp/go-build -gno-record-gcc-switches
```

可以输出指定变量信息：
```go
> go env GO111MODULE
auto

> go env GO111MODULE GOROOT
auto
c:\go
```

`-json` 标记以 JSON 格式输出：
```go
> go env -json GOROOT
{
        "GOROOT": "c:\\go"
}

// 注意参数位置，否则无法正确输出
> go env GOROOT -json
c:\go
```

`-w` 标记需要一个或多个 `NAME=VALUE` 形式的参数，并将参数指定的环境变量的默认值
更改为给定值：
```go
> go env -w GO111MODULE=on
warning: go env -w GO111MODULE=... does not override conflicting OS environment variable
```

The -u flag requires one or more arguments and unsets the default setting for the named environment variables, if one has been set with 'go env -w'.
`-u` 标记需要一个或多个参数，(恢复默认值还是???)
```go
> go env -u GO111MODULE
go env: reading go env config: open C:\Users\T460P\AppData\Roaming\go\env: The system cannot find the path specified.
```

更多环境变量, 参考 'go help environment'.
