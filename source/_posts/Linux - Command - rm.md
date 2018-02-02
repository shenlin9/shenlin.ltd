---
title: Linux - Command - rm
categories:
  - Linux
  - Command
tags:
  - rm
---

rm

<!--more-->

默认移除文件要确认
```
$ rm log1
rm: remove write-protected regular file ‘log1’? y
```

-f force 无需确认直接移除文件
```
$ rm -f log1
```

-f force 不存在的文件和参数也会被忽略
```
$ rm -f nonexistsfile asdf jlklo
```

-d dir 移除空目录
```
$ rm -d zaimusic
```

-r recursive 移除非空目录
```
$ rm -r zaimusic
```
