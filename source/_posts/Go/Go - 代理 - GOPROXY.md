---
title: Go - 代理 - GOPROXY
categories:
- Go
tags:
- Go
- GOPROXY
date: 2020-06-16 21:12:58
---

Go - 代理 - GOPROXY

<!--more-->

## 

任何可以响应指定形式 URL 的 GET 请求的 Web 服务器都可以作为 Go 模块代理(Go
module proxy)，这些 GET 请求没有查询参数，因此，即使是使用固定文件系统提供服务的
站点（URL 形式为 `file:///`）也可以作为模块代理。

发送到 Go 模块代理的 GET 请求有：

`GET $GOPROXY/<module>/@v/list` 
* 返回给定模块的版本列表，每行一个

`GET $GOPROXY/<module>/@v/<version>.info` 
* 返回指定模块指定版本的 JSON 格式的元数据

`GET $GOPROXY/<module>/@v/<version>.mod` 
* 返回指定模块指定版本的 go.mod 文件

`GET $GOPROXY/<module>/@v/<version>.zip` 
* 返回指定模块指定版本的 zip 格式封装

`GET $GOPROXY/<module>/@latest` 
* 返回指定模块最新版本的 JSON 格式的元数据，其格式和 `<module>/@v/<version>.info` 一样。
* `<module>/@latest` 是可选的，Go 模块代理也可能没有实现此功能。
* Go 命令确定模块的最新版本时，会先请求 `<module>/@v/list`，如果返回的版本列表为空或者列表中没有适用的版本时，则请求 `<module>/@latest`。

Go 命令按下列顺序确定最新版本：
* 语义版本的最新发行版
* 语义版本的最新预发行版
* 按时间顺序最近的伪版本
在 Go 1.12 和更早的版本中，Go 命令将 `<module>/@v/list` 中的伪版本视作预发行版，
但从 Go 1.13 开始不再适用。

若使用大小写敏感的文件系统来提供模块代理服务，则为了避免大小写造成的问题，
`<module>` 和 `<version>` 元素的大写是经过编码的：把每个大写字母替换为对应的小写
字母，但前面要加个感叹号，例如：`github.com/Azure` 编码为 `github.com/!azure`。

模块的 JSON 格式的元数据对应于下面的 Go 数据结构 (将来可能扩展)：

    type Info struct {
        Version string    // version string
        Time    time.Time // commit time
    }

某个模块的某个版本的 zip 归档是标准的 zip 文件，其中包含了与模块的源代码、相关文
件相对应的目录树。zip 归档文件中使用斜杠分隔路径，且每个文件路径都必须以
`<module>@<version>/` 开头，mudule 和 version 直接替换为相应的模块名和版本号，不
经过大写编码。模块目录树的根对应于 zip 归档中的 `<module>@<version>/` 前缀。

即使直接从版本控制系统下载，Go 命令也将自动合成信息、mod 文件和 zip 归档文件，并
存储到本地缓存 `$GOPATH/pkg/mod/cache/download` 中，就像是直接从模块代理中下载的
一样。

缓存的布局和代理 URL 站点的布局一致，因此把 `$GOPATH/pkg/mod/cache/download` 复
制到 `https://example.com/proxy` 站点下，其他用户就可以通过设置
`GOPROXY=https://example.com/proxy` 来进行访问缓存的模块。
