---
title: Linux - 常用命令
categories:
  - Linux
tags:
  - touch
---

Linux 常用命令

<!--more-->

【文件命名规则】
-----------------
1、大小写敏感，linux使用C语言开发，可以理解为继承了C语言的这个特性
2、除了/之外，所有的字符都合法
3、有些字符最好不要用，如空格、制表符、退格符和字符@&$#[]()-等，很可能引起混淆。
    如文件名"a b"，使用命令查看文件内容cat a b，则会误解为查看文件a和文件b的内容。
4、避免使用+-.为文件名第一个字符
	以.开始的文件为隐藏文件

    ??? + 和 - 的特殊性

    windows的文件名一般遵循8.3规则，即文件名8个字符以内，后缀名3个字符


【linux文件的后缀】
-----------------
linux文件不一定有后缀名
文件名的长度也不像windows前8后3，可能会很长


【命令格式】
-----------------
命令 -选项 参数

参数：命令操作的对象
选项：命令的不同操作方法，不同的设置
      选项可以有多个，多个可以连着写在一起，如 ls -l -a等同于 ls -la

两个特殊的目录：
.	代表当前目录
..	代表上一级目录

linux格式更严格，如上一级目录dos直接写cd..，而linux下cd和..之间必须有个空格

【命令分为了两种】

    一种是只有管理员root可以执行的命令，放在目录/sbin和/usr/sbin目录下

    一种是所有人都可以执行的命令，放在目录/bin和/usr/bin目录下

    所以可以根据命令所在的路径、目录确定都有谁可以执行此命令

    bin表示binary，二进制，sbin表示super binary???

    linux下所有的东西都是文件，命令自然也是文件

    linux下的二进制文件是个非常广泛的范畴，不是目录，不是很特殊类型的文件，就是二进制文件???

    widows用不同的图标来标明文件类型，而linux用不同的颜色来标记不同的文件类型，最常见的二进制文件和目录，分别是反色和蓝色

【数据块block】

    windows中一般用KB，MB，GB来表示大小，而linux中一般使用数据块block来表示大小，一个block通常是512字节，是linux存储数据的最小单位

    block越大，存取速度越快，越浪费空间；block越小，越节省空间，速度越慢。

【Ctrl+c终止命令】

    终止当前正在执行的命令

    限制是只有在用户能够从某个控制台上控制这个程序的时候才奏效

【使用kill根据pid终止进程】

    先使用ps命令获得pid

    kill pid

    kill -s 9 pid
        
        强制结束进程，不做善后处理

【文件处理命令】

	命令名称：ls
	英文原意：list
	所在路径：/bin/ls
	执行权限：所有用户
	功能描述：显示目录文件
	语法：ls 选项[-ald]
			-a 表示all，显示所有的文件，包括隐藏文件，两个特殊目录.和..也只有加上此参数才显示（???目录也是文件???）
			-l 表示long，以长格式显示，详细信息显示
			-d 表示directory，查看目录本身的信息，和-l选项合用，即ls -ld，否则只能看到一个.
			-i 表示inode，i节点，是一个数字标示，linux内核处理任何东西都需要知道i节点，所有文件都需要有一个i节点，两个文件的i节点可以相同

	结果解释：7个部分

            drwxr-xr-x	2	root	root	4096	12-01 21:10	 bin
            ---------------------------------------------------------

            linux中的权限分类：

                r表示read，w表示write，x表示execute执行，即可读可写可执行

            drwxr-xr-x分为4组：

                d	文件类型 d	表示directory，目录
                             -	表示二进制文件
                             l	表示link，软链接文件
                             b  
                             c
                
                rwx	所有者u(user)的权限，默认文件由谁创造，谁就是文件的所有者，但所有者权限也可转让

                r-x	所属组g(group)的权限

                r-x	其他人o(others)的权限

            其他数据含义：

                2		硬链接数
                ------------------------------------------
                root	文件的所有者	
                ------------------------------------------
                root	文件的所属组（所有者和所属组没有必然的联系吧，如所有组就是所有者所在的组???）
                ------------------------------------------
                4096	文件本身或目录及其子目录本身的大小，并非目录下所有文件的总的大小
                ------------------------------------------
                12-01 21:10	文件创建或最后修改的时间（修改属性还是内容，还是both？）
                ------------------------------------------
                bin		文件名

    注意：ls的参数即可是目录，也可是文件，因linux里的目录也是文件，所以如test目录下有很多文件，只查看文件file1的信息，可ls -l test/file1


    命令名称：cd
    英文原意：change directory
    所在路径：shell内置命令，使用which cd查找不到文件???
    执行权限：所有用户
    语法：cd 目录
    功能描述：切换目录
    举例：cd /	切换到根目录
          cd ..	回到上一级目录，中间空格不能省略


    命令名称：pwd
    英文原意：print working directory
    所在路径：/bin/pwd
    执行权限：所有用户
    语法：pwd
    功能描述：显示当前所在的工作目录
    举例：pwd


    命令名称：touch
    英文原意：
    所在路径：/bin/touch
    执行权限：所有用户
    语法：touch 文件名
    功能描述：创建新文件内容为空，文件已存在时则不创建
    举例：touch newfile

    命令名称：mkdir
    英文原意：make directories
    所在路径：/bin/mkdir
    执行权限：所有用户
    语法：mkdir 目录名
    功能描述：创建新目录
    举例：mkdir newdir
          mk /test1
          mk /test1/test11 执行此命令时test1必须存在

    命令名称：cp
    英文原意：copy
    所在路径：/bin/cp
    执行权限：所有用户
    语法：cp 一个或多个源文件 目的目录
             -R 源目录  目的目录
                复制文件无需-R参数，目录需带-R参数
             -p	同时复制创建时间等属性
    功能描述：复制文件或目录
    举例：cp file1 file2 dir1
            将文件file1、file2复制到目录dir1
          cp -R dir1 dir2
            将dir1目录复制到dir2下

    命令名称：mv
    英文原意：move
    所在路径：/bin/mv
    执行权限：所有用户
    语法：mv 源文件或目录 目的目录
    功能描述：移动文件、目录或改名文件、目录
    举例：mv file1 file3
            将文件file1改名为file3
          mv file2 dir2/file22
            将文件file2移动到目录dir2下，并改名为file22
          mv dir1 dir2/dir11
            将目录移动到目录dir2下，并改名为dir11


    命令名称：rm
    英文原意：remove
    所在路径：/bin/rm
    执行权限：所有用户
    功能描述：删除文件或目录
              删除文件无需-R/-r参数，目录需带-R/-r参数
    语法：rm file
             -f	         表示force，删除时不进行询问确认
             -r/-R dir   删除目录，其下的文件也会被删除，但若没有-f选项则每个文件都会询问是否删除

    另有rmdir命令只能删除空目录


    rmdir：只能删除空目录，使用较少
              


    命令名称：cat
    英文原意：concatenate and display files
    所在路径：/bin/cat
    执行权限：所有用户
    语法：cat 文件名
    功能描述：显示文件内容，适合显示内容不多的文件，文件内容过多时，则只能看到最后一屏的内容
    举例：cat /etc/issue


    命令名称：more
    英文原意：
    所在路径：/bin/more
    执行权限：所有用户
    语法：more 文件名
             空格	一页一页的滚动
             Enter		一行一行的滚动
             q或Q		退出
            h键调出快捷键说明，回车退出快捷键说明继续more内容的显示
    功能描述：分页显示文件内容，同时会在底部显示当前浏览的进度百分比
    举例：more /etc/services


    命令名称：head
    英文原意：
    所在路径：/bin/head
    执行权限：所有用户
    语法：head -num 文件名
    功能描述：查看文件的前num行，没有num参数则默认10行
    举例：head -20 /etc/serives

    命令名称：tail
    英文原意：
    所在路径：/bin/tail
    执行权限：所有用户
    语法：tail -num 文件名
               -f	动态显示文件的最后num行
                    如查看服务器日志信息
    功能描述：查看文件的后num行，没有num参数则默认10行
    举例：tail -20 /etc/serives


    命令名称：ln
    英文原意：link
    所在路径：/bin/ln
    执行权限：所有用户
    语法：ln -s 源文件 目标文件
             -s	创建软链接，不加此参数则为创建硬链接
    功能描述：产生链接文件
    举例：ln -s /etc/issue /etc/issue.soft
        　ln /etc/issue /ect/issue.hard

    软链接、硬链接都可以有多个

	【软链接文件特点：
	1、文件类型是l
	2、所有软链接文件的权限都是rwxrwxrwx
	3、文件名带有箭头指向源文件
	4、文件很小，只是一个符号链接
	5、时间值是创建软链接文件时的时间，和源文件时间值无关
	6、删除源文件时，软链接文件则无法正常访问
	7、可以跨文件系统(分区)

	类似于windows的快捷方式
	】

	【硬链接文件特点：
	１、文件类型、时间等和源文件相同，类似与cp -p
	２、源文件和硬链接文件是同步更新的，相当于文件的实时备份
	３、删除源文件时，硬链接文件仍可正常访问
	４、不能跨文件系统(分区，如在swap分区不能创建root分区的硬链接文件)

	可以同步更新的原因是，硬链接文件和源文件的i节点相同，可使用ls -i命令查看
	linux内核通过调用数字标识来处理各对象，如用户，文件等，所以每个文件必须都有一个i节点，但一个i节点可对应多个文件

    类似于PHP的引用变量???
	】

	目录可否做软、硬链接？

    echo "this is a test" >> file1 将输出追加到file1末尾

【权限管理命令】

		命令名称：chmod
		英文原意：change the permissions mode of a file 
		所在路径：/bin/chmod
		执行权限：所有用户
		语法：
			   chmod [{ugo}{+-=}{rwx}][文件或目录]
			   chmod [421][文件或目录]  重点

			   chmod u + r
		       chmod g - w
			   chmod o = x
			   3种对象：u所有者 g所属组 o其他人
			   3种权限：r可读   w可写   x可执行
			   3种操作：+增加权限 -删除权限 =赋值权限
			   进行组合

			   因
			   r 对应 4，w 对应 2，x 对应 1
			   chmod 421
		功能描述：改变文件或目录权限
		举例：chmod 641 file1（推荐方法）
			  chmod g+wr file1

		
				切换用户，root切换到普通用户无需密码，普通用户之间切换或普通用户切换到root需密码

                    su - 用户名
                       -l 用户名
                       --login 用户名

                添加用户

                    useradd 用户名

                为用户设置密码

                    passwd 用户名

                    这样设置密码是交互的方式，即要输入两次进行确认，在使用脚本批量添加用户并设置密码时不适用，可以使用这种方式：

                        echo 123456  | passwd --stdin ssl

                        其中123456为密码而ssl是用户名


				理解rwx对文件和目录的含义
				-----------------------------------------------------------------
				字符 权限		对文件的含义		对目录的含义
				-----------------------------------------------------------------
				r	读权限		可以查看文件内容	可以列出目录内容			
								cat、more、head		ls
								tail
				-----------------------------------------------------------------
				w	写权限		可以修改文件内容(属性呢？）可以在目录中创建、删除文件
								echo、vi			touch、mkdir、rm
				-----------------------------------------------------------------
				x	执行权限	可以执行文件		可以进入目录
								命令、脚本			cd
				-----------------------------------------------------------------
				目录的rx权限通常一起出现


		命令名称：chown
		英文原意：change file ownership
		所在路径：/bin/chown
		执行权限：所有用户
		语法：chown 用户 文件或目录
                    用户需要是系统中确实存在的用户
		功能描述：改变文件或目录的所有者
		举例：chown somebody file1
			  
	
		命令名称：chgrp
		英文原意：change file group ownership
		所在路径：/bin/chgrp
		执行权限：所有用户
		语法：chgrp 用户组 文件或目录
		功能描述：改变文件或目录的所属组
		举例：chgrp adm file1
			文件的所有者和所有组之间无必然联系，不是说文件的所有组一定是文件的所有者在的那个组


        touch创建新文件，mkdir创建新文件夹时，文件和文件夹都有缺省的权限，缺省的权限如何查询和设置？

		命令名称：umask
		英文原意：
		所在路径：/bin/umask ???which umask???查找不到
		执行权限：所有用户
		功能描述：显示或设置新建文件和目录时缺省的权限，这里不是直接设置权限数值，而是使用掩码值的方法，即777减去缺省的权限数值，即得到掩码值
		语法：

              umask 

                返回数字形式的umask码，如：

                     0022，第一个0表示特殊权限位，022表示用户权限位，是掩码值，得到正确的权限值：

                       777
                     - 022  
                     ——————
                       755  

              umask - S

                 以rwx形式显示新建文件或目录的缺省权限，如：

                    u=rwx,g=rx,o=rx

                 但有的系统可能不支持-S参数

              umask 027

                 设置新的权限掩码，要修改此值，也是使用权限的掩码值

                   777
                 - 755 
                 ——————
                   022  

	    注意；	

            linux缺省权限规则：缺省创建的文件不能授予可执行权限，所以即使默认的权限有执行权限，也只是目录有，文件没有。

            所以touch newfile3，即使umask是022，即权限值是755，文件也是没有执行权限的，实际是644。是linux的一个小安全机制，可防止病毒、木马等文件。 


【文件搜索命令】
		
		命令名称：which
		英文原意：
		所在路径：/usr/bin/which
		执行权限：所有用户
		语法：which 命令名称
		功能描述：只查找命令目录，显示命令所在的绝对路径
                  同时也会显示命令的别名，如ls命令其实并不带颜色输出，其颜色是使用别名功能实现的
		举例：which ls

		命令名称：whereis
		英文原意：
		所在路径：/usr/bin/which
		执行权限：所有用户
		语法：whereis 命令名称
		功能描述：只查找命令目录，显示命令所在的绝对路径
                  同时也会显示命令帮助文档所在的目录
		举例：whereis ls

		命令名称：find
		英文原意：
		所在路径：/usr/bin/find
		执行权限：所有用户
		语法：find 搜索路径 搜寻关键词
		功能描述：通用查找命令，可查找任何的文件或目录（which和whereis只查找命令所在的路径），Ctrl-c中断查找
                  查找时尽量节省系统资源，查找范围越小越好，关键词越精确越好
		举例：

            find 搜索路径

                遍历并显示搜索路径下的所有子文件夹和文件

                    find /root 

            选项 -name 

                根据文件名查找

                    find /etc -name init 查找文件名为init的文件

                                         可配合通配符，* 匹配任意字符，包含0个字符 

                                                       ? 匹配单个字符，不包含0个字符（和正则表达式的包含0个或1个字符不同???）

            选项 -size

                根据文件大小查找

                    find / -size +204800 在根目录下查找大于100MB的文件 慎用根目录查找

					     size后的数字单位为block数据块，一个block数据块为512字节，即0.5KB

                         1KB = 2Block
					     100MB = ?Block
					     100MB = 102400KB = 204800Block
					
					     大于 + 小于 -

                         注意：根据大小查找时都需要配合+-，如不可能文件的大小刚刚好等于100MB

            选项 -ctime/atime/mtime/cmin/amin/mmin

                根据时间查找

                    c - change 改变，文件属性被修改，如所有者、所属组、权限
                    a - access 访问，文件内容被浏览过
                    m - modify 修改，文件的内容被修改过

                    以天为单位：ctime、atime、mtime
                    以分钟为单位：cmin、amin、mmin

                    - 多少时间之内
                    + 超过多少时间

                    注意：根据时间查找时都需要配合+-，如不可能文件的修改时间等刚刚好等于2个小时

                    find /etc -mmin -120 两个小时之内被修改过的内容的文件，注意-120前的-表示“xx时间之内”，而不是选项前的-

            选项 -type

                根据文件类型查找：f 二进制文件 l 软链接文件 d 目录

                    find /etc -type f

            选项 -user

                根据文件所有者查找

                    find /home -user samle

			连接符-a和-o

				-a and 逻辑与，即查找的两个条件必须都满足
				-o or  逻辑或，即查找的两个条件满足一个即可
				
					find /etc -name init* -a -type f 只列出文件，不列出目录(f 文件 d 目录 l 软链接)

            连接符-exec 命令 {} \;

                {}表示find查询的结果
                \ 转义符，使符号或命令使用本身的含义，如rm有个别名rm -i，删除时会提示，但执行\rm，则不会再提示
            
                find /test -name testfile3 -exec rm {} \; 查找到file3后将其删除
                find /home -user samelee -exec rm -rf {} \; 找到samlee的文件并将其删除

            连接符-ok 命令 {} \;

                同上述exec相同，只是执行命令时会增加询问

            选项 -inum

				根据i节点进行删除，特殊的文件名，如a空格b  -abc 在删除时会遇到问题，可使用find和i节点进行删除
					
				ls -i
				find . -inum 16 -exec rm {} \;

                创建a空格b文件的方法：touch "a b"，直接touch a b会被创建两个文件a和b，直接rm a b会被认为要删除文件a和b，正确的删除rm "a b"

                创建-abc文件的方法：touch -- -abc，rm -abc和rm "-abc"都会被认为是option，可以rm -- -abc，rm没有根据i节点删除文件的选项，所以需要配合ls和find


		指令名称：locate
		指令英文原意：list files in databases
		指令所在路径：/usr/bin/locate
		执行权限：all user
		语法：locate [搜索关键字]
		功能描述：寻找文件或目录，Unix系统不提供此
			 (find是检索文件系统，而locate是在文件数据库中查找，所以速度很快，但因为文件数据库定期更新，所以查找结果可能会不太准确，可配合updatedb命令使用。适合搜索系统的命令、配置文件等默认自带的文件）
		范例：locate file（和file相关的文件，搜索结果疑惑???）
		

		指令名称：updatedb
		指令英文原意：update the slocate dababase
		指令所在路径：/usr/bin/updatedb
		执行权限：root
		语法：updatedb
		功能描述：建立整个系统目录文件的数据库，可配合计划任务，定期执行updatedb
		范例：updatedb


		指令名称：grep
		指令英文原意：global search regular expression(RE) and print out the line,全面搜索正则表达式并把行打印出来
                      是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。
		指令所在路径：/bin/grep
		执行权限：all user
		语法：grep [指定字符串] [源文件]
              grep -v 字符串 源文件     找出不匹配的行
		功能描述：在文件中搜寻字串匹配的行输出
		范例：grep ftp /etc/services
              grep -v "^#" /etc/inittab | more 剔除inittab中的以#开头的注释行


【帮助命令】

		命令 --help
            主要获取命令的选项信息

		help [内置命令]
            查看shell内置命令的帮助，如help cd


		指令名称：man
		指令英文原意：manual
		指令所在路径：/usr/bin/man
		执行权限：all user
		语法：man [命令或配置文件]
		功能描述：
        获得帮助信息，调用了more命令来浏览长内容
        man page 是 linux 最常用的参考手册，由很多页面组成，每个页面描述一个主题，页面又被组成了 9 个 Section

		范例：
            man ls          查看ls命令的帮助信息
			man services    查看配置文件services的帮助信息，只能是配置文件，无需写路径
            man cd          因为cd是shell的一个内置命令，所以man列出的是当前shell的所有内置命令，而不是关于cd命令的说明
			当命令和配置文件名称相同时，如，passswd，man会优先查看命令的帮助

            man ls
            输出中包含的是：LS(1)
            LS表示命令，后面的(1)也是有含义的，表示的是帮助的种类
			帮助分成很多种，共9种，第1种是命令的帮助，第5种是配置文件的帮助，所以查看配置文件可以写成 man 5 passwd
                1. User Commands 普通用户命令，如 ls(1)
                2. System Calls 系统调用，如 _exit(2)
                3. C Library Functions 函数和函数库，多为C语言的，如 printf(3)
                4. Devices and Special Files 设备文件的说明，通常是在/dev下的文件，如 null(4) 描述的是设备文件 /dev/null 的作用
                5. File Formats and Conventions配置文件或者是某些文件的格式，如 password(5) 描述的是 /etc/passwd 文件的格式
                6. Games 游戏
                7. Miscellanea 杂项，包括了惯例与协议等，例如Linux文件系统、网络协议、ASCII code等说明
                8. System Administration tools And Daemons 具有 root 权限用户的系统管理命令，如 ifconfig(8)
                9. 跟kernel有关的文件
            1、5、7是常用的
            2、3是程序开发需要用的

            注意区分用户命令1和系统管理命令8，
            用户命令一般位于 /bin /usr/bin 目录，由普通用户调用
            系统管理命令一般位于 /sbin /usr/sbin，由具有 root 权限用户调用

        h键可调出怎样在man中操作的帮助文档，如：f 向前滚动一页 
                                                b 向后滚动一页 或者 空格
                                                j 向下滚动一行 或者 回车
                                                k 向上滚动一行
                                                像在vi中一样使用“/string”或者“?string”来查询string字符串，用n（或N）继续查询下一个（或上一个）。
                                                还有按q退出。。。
        有时man xxx时提示“No manual entry for”
        则表示xxx命令没有man文件或者man命令不知道xxx命令的man文件路径
        没有man文件的无能为力，不知道路径的可以这样解决：
            第一种：编辑/etc/man.config文件，添加MANPATH路径，如MANPATH /usr/local/xxx/man/。
            第二种：不修改man.config文件，直接man后面加上绝对路径，如：man /usr/local/varnish/share/man/man7/vcl.7 
            第二种方法适合用无root权限的用户。

		指令名称：info
		指令英文原意：information
		指令所在路径：/usr/bin/info
		执行权限：all user
		语法：man [任何关键字]
		功能描述：获得帮助信息，Unix没有此命令
		范例：  info ls 查看ls命令的帮助信息
        info快捷键：
                    空格      向后翻页
                    backspace 向前翻页
                    n         后一个章节
                    p或u      前一个章节


		指令名称：whatis
		指令英文原意：
		指令所在路径：/usr/bin/whatis
		执行权限：all user
		语法：whatis [任何关键字]
		功能描述：获得索引的简短说明信息，在whatis数据库中搜寻完整匹配关键字，提取了"man 命令"中的name一行，是命令的一个简短说明
		范例：whatis ls 查看ls命令的帮助信息

		指令名称：apropos
		指令英文原意：
		指令所在路径：/usr/bin/apropos
		执行权限：all user
		语法：apropos [任何关键字]
		功能描述：
		范例：apropos ls 查看ls命令的帮助信息，相当于man -k

                查看配置文件的内容 ，如apropos 配置文件名


		指令名称：makewhatis
		指令英文原意：
		指令所在路径：/usr/sbin/makewhatis
		执行权限：root
		语法：makewhatis
		功能描述：建立whatis和apropos搜索使用的whatis数据库，当使用这两个命令发生错误时，就是whatis database没有建立
        ???不知道怎么退出命令，怎么使用这个命令


【压缩解压命令】

        linux的压缩格式，windows都支持；而windows的压缩格式，linux不一定支持，如.rar

        linux最常使用的.gz和.bz2格式，都可以使用tar命令一步到位完成归档压缩、解压缩

        其-a参数会根据后缀名自动调用gzip/gunzip/bzip2/bunzip2等命令完成

        处理.zip文件还是需要zip/unzip命令

        tar Tape ARchive，磁带存档

            归档压缩
            ---------
            $tar -cavf testfile.tar.gz testfile1 testfile2 testfile3
            $tar -cavf testfile.tar.bz2 testfile1 testfile2 testfile3
                -c 创建新归档文件
                -a 使用归档后缀名来决定压缩程序，gzip还是bzip2，不带此参数则仅打包不压缩
                -v 详细地列出处理的文件
                -f 必须有的参数，因为后面是文件名
            注：归档后和归档前的文件名都不能省略，因为大多数都是多个文件归档为一个文件，多对一关系


            解压缩
            --------
            $tar -xvf testfile.tar.gz
            $tar -xvf testfile.tar.bz2
            $tar -xvf testfile.tar.bz2 -C testdir
                -x 展开归档文件，处理.tar
                -v 详细地列出处理的文件
                -f 必须有的参数，因为后面是文件名

                -C 改变解出文件目录，
                   因为大部分的软件包都是把顶层文件夹直接打包，
                   所以解压缩后自动在当前目录下生成此顶文件夹，
                   故多数不需要此参数
                   若是直接打包的多个文件，则最好给出此参数
                   使文件统一解压到一个文件夹里

            和归档后缀名无关，.gz和.bz2都可以解压



            查看归档文件列表
            ------------
            $tar -tvf archive.tar
                -t 列出归档内容
                和归档后缀名无关，.gz和.bz2都可以显示


        ------------------------------------------------------------------------------------

		命令名称：gzip
		英文原意：GNU zip
		所在路径：/bin/gzip
		执行权限：所有用户
		语法：gzip 选项 文件
		功能描述：压缩文件，压缩后文件格式.gz
		举例：gzip newfile4
		注意：1、不能压缩目录，只能压缩文件（不能压缩软链接文件），可以先使用tar命令将目录打包为文件
			  2、不保留原文件


		命令名称：gunzip 或 gzip -d
		英文原意：GNU unzip
		所在路径：/bin/gunzip
		执行权限：所有用户
		语法：gunzip 选项 文件
		功能描述：解压缩.gz的压缩格式文件
		举例：gunzip newfile4 或 gzip -d newfile4


		命令名称：tar
		英文原意：
		所在路径：/bin/tar
		执行权限：所有用户
		语法：tar 选项 打包完后的文件名 源文件名
				  -c  表示create,产生.tar打包文件
				  -x  解包.tar文件

				  -v  显示命令执行过程的详细信息
				  -f  指定目标文件名，必须包含此选项
                  -z  --gzip --gunzip 通过gzip过滤归档
                  -j  --bzip2         通过bzip2过滤归档
		功能描述：打包目录，将目录打包为一个文件，打包后文件格式：.tar，若打包同时进行压缩，则文件格式.tar.gz
		举例：tar -cfzv newdir.tar.gz newdir    ???选项顺序还有讲究，这个不行
                  -zcvf                                              这个行
		      tar -xfzv newdir.tar.gz
		注意：打包并压缩的文件名后缀可为任意，也可没有，但.tar.gz容易辨认
		      file命令可帮助判断文件的类型，如file newdir.asdf
		另：
			  软件包3种安装形式：
				  1、二进制包  
				  2、源代码包  先编译再安装，虽麻烦，但定制性高
				  3、脚本安装




		命令名称：zip
		英文原意：
		所在路径：/usr/bin/zip
		执行权限：所有用户
		语法：zip 选项	压缩后文件或目录	源文件或目录
					-r	压缩目录
		功能描述：压缩文件或目录
		举例：zip newfile4.zip	newfile4
			  zip -r newdir.zip	newdir
		注意：1、会保留原文件
			  2、会显示压缩比
			  3、.zip默认为windows和linux的通用格式


		命令名称：unzip
		英文原意：
		所在路径：/usr/bin/unzip
		执行权限：所有用户
		语法：unzip 压缩文件
		功能描述：解压缩.zip的压缩格式文件
		举例：unzip test.zip

		命令名称：bzip2
		英文原意：
		所在路径：/usr/bin/bzip2
		执行权限：所有用户
		语法：bzip2 选项 文件
					-k	产生压缩文件后保留原文件
		功能描述：压缩文件
		举例：bzip2 -k file1
		注意：1、压缩后文件格式 .bz2
			  2、.bz2是.gz的升级版，也只能压缩文件，但-k参数可保留原文件
			  3、压缩比很高

		命令名称：bunzip2
		英文原意：
		所在路径：/usr/bin/bunzip2
		执行权限：所有用户
		语法：bunzip2 选项 压缩文件
					-k	保留原压缩文件
		功能描述：解压缩文件
		举例：bunzip2 -k file1.bz2

【网络通信命令】

		命令名称：write
		英文原意：
		所在路径：/usr/bin/write
		执行权限：所有用户
		语法：write 用户名
		功能描述：向另外一个用户发信息，以Ctrl+D为结束
		举例：write samlee
		注意：为实时通信，双方必须都已登录

		命令名称：wall
		英文原意：write all
		所在路径：/usr/bin/wall
		执行权限：所有用户
		语法：wall 信息 文件名
		功能描述：向所有用户广播信息
		举例：wall happy new year

		命令名称：ping
		英文原意：
		所在路径：/usr/sbin/ping
		执行权限：root
		语法：ping 选项 IP地址
					-c 指定ping的次数
					-s 指定包的字节大小，最大65507，默认为64字节
		功能描述：测试网络连通性，主要查看延时和丢包率两个指标（低端的测线仪只能测试是否联通，不能测试丢包率）
		举例：ping 192.168.0.1
		注意：和windows的ping不同，linux下的ping命令会一直ping下去，除非按ctrl+c终止

			   ping 局域网其他机器地址，正常则证明局域网没问题
			   ping 本机Ip地址，正常则证明本地的IP设置没问题
			   ping 回环地址127.0.0.1，正常则证明本机的tcp/ip协议安装没问题，即使没有网卡，只要正确安装tcp/ip协议，一样可以ping通


		命令名称：ifconfig
		英文原意：interface config
		所在路径：/usr/sbin/ifconfig
		执行权限：root
		语法：ifconfig 选项 网卡设备标识
						-a	显示所有网卡信息（linux可省略，unix不可）
		功能描述：查看网络设置信息
		举例：ifconfig -a
			更改	
			  ifconfig etho 192.168.0.2 只在本次会话生效，未写入配置文件，下次重启即失效
			查询
			  ifconfig eth0

        eth0    第一块物理网卡

        lo      回环网卡(127.0.0.1)

【系统关机命令】

		命令名称：shutdown
		英文原意：
		所在路径：/usr/sbin/shutdown
		执行权限：root
		语法：shutdown
		功能描述：关机
		举例：shutdown -h now（立即关机，真正服务器环境慎用）


		命令名称：reboot
		英文原意：
		所在路径：/usr/sbin/reboot
		执行权限：root
		语法：reboot
		功能描述：重启
		举例：reboot

【Shell应用技巧】

	cat /etc/shells 列出当前系统中的shell

	[root@localhost test]# 为bash提示符

	命令补全

		输入命令或文件名时，可使用tab键进行补全

        匹配的命令不止一个时，连按两下tab

        当在shell命令行一个字符也没有输入时，连按两下tab，则会提示: Display all 823 possibilities?( y or n)

            因为没有给出任何字符提示，所以shell问要不要把所有可能的commands,files,directories都列出来

            823是上面所有可能的总数，每个系统的这个数字会不同

	清屏
		clear命令 或 快捷键 ctrl-l

	清除光标前所有字符

		快捷键 ctrl-u

	命令历史

		history命令，!加上命令序号可再次执行此命令

		上下箭头可切换执行过的命令

	命令别名

		alias命令可显示所有命令的别名

		别名可使命令更容易记忆，如alias copy = cp

		命令别名定义：alias 别名=命令
		              alias 别名="命令 -选项"
						带选项时需要双引号括起来
		删除别名：unalias 别名
			
	输入/输出重定向

		同标准I/O一样，Shell对于每一个进程预先定义3个了文件描述字（0、1、2）。分别对应于：
			0	(STDIN)		标准输入；指的是键盘
			1	(STDOUT)	标准输出；指的是显示器
			2	(STDERR)	标准错误输出；指的是显示器

		输出重定向：> 和 >>
			>	将内容 覆盖 输出到指定的设备或文件
			>>	将内容 追加 输出到指定的设备或文件

		输入重定向：<
			如：wall < /hello.msg

		错误输出重定向：2>
			如：cp -R /usr /backup/usr.bak 2> /bak.error
			有无 2>>

        输出到/dev/null表示不处理输出???

            /bin/grep ssl /etc/passwd > /dev/null 2> /dev/null

	管道

		将一个命令的输出传送给另一个命令，作为另一个命令的输入。

		使用方法：命令1|命令2|命令3|……

		范例：ls -l /etc | more
			  ls -l /etc | grep init
			  ls -l /etc | grep init | wc -l

        wc 计数器

            wc -l /etc/services 统计行数

	命令连接符

		;
			用;间隔的各命令按顺序依次执行,执行结果顺序输出，如linux内核编译时可让那几个固定的编译命令顺序执行而无需等待
		&&
			前后命令的执行存在逻辑与关系，只有&&前面的命令执行成功后，它后面的命令才被执行
		||
			前后命令的执行存在逻辑或关系，只有||前面的命令执行失败后，它后面的命令才被执行

	命令替换符

		`
		不是'，是1旁边的那个
		将一个命令的输出作为另一个命令的参数
		格式为：命令1 `命令2`
		举例：ls -l `which touch`	等价于	ls -l /bin/touch

	bash
	
		指的是bashell,位于/bin/bash

		上下箭头可上下翻页已输入过的命令

		命令补全功能：tab键自动补全，包括补全命令和文件等

		Ctrl + l 清屏，等同于clear命令

		Ctrl + u 将光标前的所有字符删除

		history 会记录执行过的所有命令,使用!188可执行188号命令


绝对路径：从根开始一级一级进入各子目录，最后指定该命令或文件
相对路径：从当前目录为参照点进入某目录


