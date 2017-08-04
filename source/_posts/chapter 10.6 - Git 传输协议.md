title: chapter 10.6 - Git 传输协议
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - GitHub

---

Git 可以通过两种主要的方式在版本库之间传输数据：“哑（dumb）”协议和“智能（smart）”协议。

<!--more-->

## “哑”协议

这个协议之所以被称为“哑”协议，是因为在传输过程中，服务端不需要有针对 Git 特有的代码；抓取过程是一系列 HTTP 的 GET 请求，

这种情况下，客户端可以推断出服务端 Git 仓库的布局。

```
$ git clone 
```

http-fetch 过程

## 智能协议
