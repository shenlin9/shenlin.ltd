# vim 设置 python 环境

Tags ： AAA vim python

---

vim 里执行命令输出如下说明支持 python
```
:version
...+python/dyn +python3/dyn..
```

vim 里执行命令输出如下说明已安装 python 环境
```
:echo has("python")
1

:echo has("python3")
1
```
* 0 表示未安装 python 环境
* 还发现个问题就是桌面上的文件使用 vim 打开，执行上述命令时返回0，其他目录则返回1
* 未安装 python 环境，要先安装 python 再安装 vim
* python 不要安装 3.6 版本，vim 不支持，安装 3.5 版本即开


