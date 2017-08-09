title: chapter 7.5 - Git 搜索
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - Git Grep

---

Git 搜索用于查找方法的定义或调用，或者方法的变更历史等。

<!--more-->

## Git Grep

git grep 用于在提交历史或工作目录中查找字符串或正则表达式 ，

git grep 相对于 grep、 ack等常用搜索命令的优点：

第一就是速度非常快，

第二是不仅仅可以可以搜索工作目录，还可以搜索历史记录。 

常用搜索选项

```
$ git grep linux
source/_posts/NeoVim NyaoVim.md:linux 位置：`$HOME\.config\nvim\init.vim`
source/_posts/SpaceVim.md:linux
source/_posts/chapter 1.2 - git 起步.md:    linux: /etc/gitconfig
source/_posts/chapter 1.2 - git 起步.md:   linux:  ~/.gitconfig 或 ~/.config/git/config
```

`-n` 参数显示行号
```
$ git grep -n linux
source/_posts/NeoVim NyaoVim.md:23:linux 位置：`$HOME\.config\nvim\init.vim`
source/_posts/SpaceVim.md:29:linux
source/_posts/chapter 1.2 - git 起步.md:117:    linux: /etc/gitconfig
source/_posts/chapter 1.2 - git 起步.md:125:   linux:  ~/.gitconfig 或 ~/.config/git/config
```

`--count` 显示概述信息：包含匹配的文件以及包含多少匹配
```
$ git grep --count linux
source/_posts/NeoVim NyaoVim.md:1
source/_posts/SpaceVim.md:1
source/_posts/chapter 1.2 - git 起步.md:2
```

`--break` 在不同的文件之间加入空行分割
`--heading` 同一个文件中的匹配行只显示一次文件名
```
$ git grep --break --heading linux
source/_posts/NeoVim NyaoVim.md
linux 位置：`$HOME\.config\nvim\init.vim`

source/_posts/SpaceVim.md
linux

source/_posts/chapter 1.2 - git 起步.md
    linux: /etc/gitconfig
   linux:  ~/.gitconfig 或 ~/.config/git/config
```

`-p` 显示匹配的行属于哪个函数或方法
```
$ git grep -p gmtime_r *.c
date.c=static int match_multi_number(unsigned long num, char c, const char *date, char *end, struct tm *tm)
date.c:         if (gmtime_r(&now, &now_tm))
date.c=static int match_digit(const char *date, struct tm *tm, int *offset, int *tm_gmt)
date.c:         if (gmtime_r(&time, tm)) {
```

`-e <pattern>` 匹配正则，`--and`, `--or`, `--not`, `(` `)`都是和`-e`搭配使用

搜索在旧版本 1.8.0 的 Git 代码库中定义了常量名包含 “LINK” 或者 “BUF_MAX” 这两个字符串所在的行
```
$ git grep --break --heading -n -e '#define' --and \( -e LINK -e BUF_MAX \) v1.8.0

v1.8.0:builtin/index-pack.c
62:#define FLAG_LINK (1u<<20)

v1.8.0:cache.h
73:#define S_IFGITLINK  0160000
74:#define S_ISGITLINK(m)       (((m) & S_IFMT) == S_IFGITLINK)

v1.8.0:environment.c
54:#define OBJECT_CREATION_MODE OBJECT_CREATION_USES_HARDLINKS

v1.8.0:strbuf.c
326:#define STRBUF_MAXLINK (2*PATH_MAX)

v1.8.0:symlinks.c
53:#define FL_SYMLINK  (1 << 2)
```

## 日志搜索

`-S` 显示新增和删除字符串的提交
```
$ git log -Slinux --oneline

d313f45 继续写笔记
9c789ce 增加 vim 相关文章
f9e4a80 '笔记继续更新，增加了好多文件'
ee6d6db 继续完成 hexo 相关文章
bae99ca 添加目前所有的Markdown文件
ad1a8e5 hexo init
```

`-G` 正则表达式搜索
```
```

`-L` 行日志搜索，可以展示代码中一行或者一个函数的历史

如查看 `zlib.c` 文件中 `git_deflate_bound` 函数的每一次变更
```
$ git log -L :git_deflate_bound:zlib.c
```
也可以提供正则搜索，等价于上面的命令
```
git log -L '/unsigned long git_deflate_bound/',/^}/:zlib.c
```
或提供单行或者一个范围的行号来获得相同的输出
```
```


