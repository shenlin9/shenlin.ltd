---
title: linux - tar
categories:
  - Linux
  - Command
tags:
  - Linux
  - tar
---

tar

首先要弄清两个概念：打包和压缩。打包是指将一大堆文件或目录变成一个总的文件；压缩
则是将一个大的文件通过一些压缩算法变成一个小文件。

为什么要区分这两个概念呢？这源于Linux中很多压缩程序只能针对一个文件进行压缩，这
样当你想要压缩一大堆文件时，你得先将这一大堆文件先打成一个包（tar命令），然后再
用压缩程序进行压缩（gzip bzip2命令）。

<!--more-->

## 概述

1. 压缩和解压缩都使用 tar 命令
2. 创建压缩文件使用 -c 参数，解压缩使用 -x 参数
3. 不同的压缩格式还需要安装对应的压缩软件如 bzip2，gzip 等，相应的也需要使用额外
   的参数指明使用的压缩软件如 -j 或 -z 等。
4. 默认解压出来的文件会覆盖已存在的同名文件，很危险，可以使用 -k 参数确保不会错
   误覆盖
5. -f 用于指定目标和原文件，且必须是最后一个参数

## 参数

tar

下面五个是独立的命令，可以和别的命令连用但只能用其中一个

-c, --create: 建立压缩档案
-x, --extract：解压
-t, --test-label：查看内容(测试压缩卷标并退出)
-r, --append：向压缩归档文件末尾追加文件
-u, --update：更新原压缩包中的文件

下面的参数是根据需要在压缩或解压档案时可选的。

-z：使用 gzip 程序过滤文件
-j：使用 bzip2 程序过滤文件
-Z：使用 compress 程序过滤文件
-a：更智能，根据文件的后缀名自动判断调用的程序

-v：显示所有过程
-O：将文件解开到标准输出
-C：指定解压到的目录

-m, --touch: 不提取文件修改时间
-k, --keep-old-files: 不覆盖已存在文件而提示错误
-p, --preserve-permissions: 保留文件权限，这个属性是很重要的，尤其是当您要保留原
    本文件的属性时。

下面的参数-f是必须的
-f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。

## 举例

`tar -cf all.tar *.jpg`
将所有.jpg的文件打成一个名为all.tar的包。-c是表示产生新的包，-f指定包的文件名。

`tar -rf all.tar *.gif`
将所有.gif的文件增加到all.tar的包里面去。-r是表示增加文件的意思。

`tar -uf all.tar logo.gif`
更新原来tar包all.tar中logo.gif文件，-u是表示更新文件的意思。

`tar -tf all.tar`
列出all.tar包中所有文件，-t是列出文件的意思

`tar -xf all.tar`
解出all.tar包中所有文件，-x是解开的意思

`tar -zcvpf log31.tar.gz log2014.log log2015.log log2016.log`
文件备份下来，并且保存其权限


## 压缩

    tar -cvf jpg.tar *.jpg //将目录里所有jpg文件打包成tar.jpg 

    tar -zcvf jpg.tar.gz *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz

    tar -jcvf jpg.tar.bz2 *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2

    tar -Zcvf jpg.tar.Z *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z

    rar a jpg.rar *.jpg //rar格式的压缩，需要先下载rar for linux

    zip jpg.zip *.jpg //zip格式的压缩，需要先下载zip for linux

## 解压

    tar -xvf file.tar //解压 tar包

    tar -zxvf file.tar.gz //解压tar.gz

    tar -jxvf file.tar.bz2   //解压 tar.bz2

    tar -Zxvf file.tar.Z   //解压tar.Z

    unrar e file.rar //解压rar

    unzip file.zip //解压zip

## 智能通用：

    tar -acvf file.tar.gz *.jpg
    tar -acvf file.tar.bz2 *.jpg
    tar -acvf file.tar.Z *.jpg
    tar -cvf file.tar *.jpg

    tar -xvf file.tar.gz
    tar -xvf file.tar.bz2
    tar -xvf file.tar.Z
    tar -xvf file.tar

    经测试压缩时必须使用 -a 参数，搭配文件的后缀名，会自动选择程序进行压缩，否则
    少了 -a 参数则只打包不压缩；解压缩时则不需要使用 -a 参数，因为 -x 参数就表示
    解压

## 总结

1、*.tar 用 tar -xvf 解压

2、*.gz 用 gzip -d或者gunzip 解压

3、*.tar.gz和*.tgz 用 tar -xzf 解压

4、*.bz2 用 bzip2 -d或者用bunzip2 解压

5、*.tar.bz2用tar -xjf 解压

6、*.Z 用 uncompress 解压

7、*.tar.Z 用tar -xZf 解压

8、*.rar 用 unrar e解压

9、*.zip 用 unzip 解压

## bzip2 提示出错

下面的错误虽然是提示 No such file or directory 但其实是因为没有安装 bzip2，其他
压缩软件如 compress 也会因为没安装提示一样的错误：
```bash
➜  tar -cjf 1.tar.bz2 1.txt
tar (child): bzip2: Cannot exec: No such file or directory
tar (child): Error is not recoverable: exiting now
tar: Child returned status 2
tar: Error is not recoverable: exiting now

➜  test tar -cZvf 1.tart.Z 1.txt
1.txt
tar (child): compress: Cannot exec: No such file or directory
tar (child): Error is not recoverable: exiting now
tar: Child returned status 2
tar: Error is not recoverable: exiting now

➜  sudo yum install -y bzip2
```

## tar --help

  -a, --auto-compress        use archive suffix to determine the compression
                             program
  -j, --bzip2                filter the archive through bzip2
  -z, --gzip, --gunzip, --ungzip   filter the archive through gzip
  -Z, --compress, --uncompress   filter the archive through compress

  -I, --use-compress-program=PROG
                             filter through PROG (must accept -d)
  -J, --xz                   filter the archive through xz
      --lzip                 filter the archive through lzip
      --lzma                 filter the archive through lzma
      --lzop
      --no-auto-compress     do not use archive suffix to determine the
                             compression program


  -p, --preserve-permissions, --same-permissions
                             extract information about file permissions
                             (default for superuser)
      --preserve             same as both -p and -s
      --same-owner           try extracting files with the same ownership as
                             exists in the archive (default for superuser)

  -s, --preserve-order, --same-order
                             member arguments are listed in the same order as
                             the files in the archive

  -k, --keep-old-files       don't replace existing files when extracting,
                             treat them as errors
      --keep-directory-symlink   preserve existing symlinks to directories when
                             extracting
      --keep-newer-files     don't replace existing files that are newer than
                             their archive copies
      --no-overwrite-dir     preserve metadata of existing directories
      --overwrite            overwrite existing files when extracting
      --overwrite-dir        overwrite metadata of existing directories when
                             extracting (default)
      --recursive-unlink     empty hierarchies prior to extracting directory
      --remove-files         remove files after adding them to the archive
      --skip-old-files       don't replace existing files when extracting,
                             silently skip over them

  -m, --touch                don't extract file modified time
      --no-delay-directory-restore
                             cancel the effect of --delay-directory-restore
                             option
      --no-same-owner        extract files as yourself (default for ordinary
                             users)
      --no-same-permissions  apply the user's umask when extracting permissions
                             from the archive (default for ordinary users)
      --numeric-owner        always use numbers for user/group names
      --owner=NAME           force NAME as owner for added files

