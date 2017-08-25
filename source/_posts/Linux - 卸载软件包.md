

make clean
清除上次的make命令所产生的object文件（后缀为“.o”的文件）及可执行文件。
make distclean
类似make clean，但同时也将configure生成的文件全部删除掉，包括Makefile。

看一下makefile里面有没有make uninstall选项。 
如果没有 
看 makefile 里的 make install ，做了什么操作（基本就是复制文件，创建链接），手工处理
