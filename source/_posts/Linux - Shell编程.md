【Shell结构】

    #!  指定执行脚本的shell

    #   注释行

【创建shell程序的步骤】

    第一步：创建换一个包含命令和控制结构的文件。

    第二步：修改这个文件的权限使它可以执行。chmod u+x

    第三步：执行./example，也可以使用sh example执行

【shell变量】

    两类变量：临时变量和永久变量

    临时变量是shell程序内部定义的，其实也范围仅限于定义它的程序，对其他程序不可见，包括：用户自定义变量、位置变量。

    永久变量是环境变量，其值不随shell脚本的执行结束而消失。

        $PATH   $LANG   $SHELL  $PS1

【用户自定义变量】

    由字母或下划线开头，由字母，数字，下划线序列组成。

    区分大小写，但习惯上用大写字母来命名变量。

    变量名长度没有限制。

【定义、使用和删除变量】

    定义和删除变量时，不使用$，但使用变量时，要在变量名前加前缀$。即给变量赋值时不加$，从变量取值时需要加$。

    变量第一次使用时即被定义，这时需要对变量进行赋值。

    变量赋值时，赋值号"="的两边不能有空格。赋值举例：

        定义时赋值：NUM=1

        将一个命令的执行结果赋给变量：TIME=`date`

        将一个变量赋给另一个变量：A=$B

                                  TIME=$(date +%F)

        查看变量值：echo $A

    删除变量：unset 变量名

              unset A

    查看所有变量，包含了环境变量和用户的自定义变量：set

【位置变量】

    Shell解释执行用户命令时，将命令行的第一个部分作为命令名，其他部分作为参数。

    由出现在命令行上的位置确定的参数称为位置参数。例如：

        ls -l file1 file2 file3

            $0  这个程序的文件名ls -l

            $n  这个程序的第n个参数值，n=1-9

【特殊变量】

    $*  这个程序的所有参数

    $#  这个程序的参数个数

    $$  这个程序的PID

    $!  执行上一个后台命令的PID

    $?  上一个命令执行是否成功，取值0或非0，0表示执行成功，非0表示不成功。成功不成功不是执行有没有错误，如grep sss /etc/passwd没有找到结果也表示执行不成功

【单引号和双引号】

    双引号中的变量会被解析，单引号中的变量不被解析

    ssl=123

    echo "adf $ssl";

    echo 'adf $ssl';

【Shell命令】

    read VAR：从键盘读入数据赋给变量VAR

        #read $VAR1 $VAR2 $VAR3

        #1 2 3

    expr命令：对整型变量进行算术运算，+-*/运算符两侧都必须有空格，除法结果为小数时会取整为0，乘法因为???原因需要转义

        expr 3 + 5

        expr $var - 5

        expr $var \* 10

        expr $var / $var2

        和命令替换符 ` 配合使用： 

        expr `expr $var1 + $var2` / $var4

        var4=`expr $var1 / $var2`

【变量测试语句】

    用于测试变量是否相等，是否为空，文件类型等

    格式：test 测试条件

    测试范围：整数、字符串、文件

    字符串测试：

        test str1=str2  测试字符串是否相等

        test str1!=str2 测试字符串是否不相等

        test str1       测试字符串是否不为空

        test -n str1    测试字符串是否不为空

        test -z str1    测试字符串是否为空

    整数测试：

        test int1 -eq int2  测试整数是否相等

        test int1 -ne int2  测试整数是否不相等

        test int1 -ge int2  测试int1是否>=int2

        test int1 -gt int2  测试int1是否>int2

        test int1 -le int2  测试int1是否<=int2

        test int1 -lt int2  测试int1是否<int2

    文件测试：

        test -d file    测试文件是否为目录

        test -f file    测试文件是否为常规文件

        test -x file    测试文件是否可执行

        test -r file    测试文件是否可读

        test -w file    测试文件是否可写

        test -a file    测试文件是否存在

        test -s file    测试文件大小是否为0

    变量测试语句一般不单独使用，常作为if语句的测试条件，如：

        if test -d $1 then
            ...
        fi

    变量测试语句可用[]进行简化，[ 右侧 ] 左侧有空格，如：

        test -d $1 等价于 [ -d $1 ]

【sh -x 脚本】

    -x  此选项使得脚本执行时，会将执行的每一行输出到屏幕上，并在前面加上+号，便于调试

【条件判断if】

    then语句必须另起一行

    if语句

        if 判断条件
        then
            ...执行语句
        fi

    if..else语句

        if 判断条件
        then
            ...执行语句
        else
            ...执行语句
        fi

    if..elif..elif..else语句

        if 判断条件
        then
            ...执行语句
        elif 判断条件
        then
            ...执行语句
        elif 判断条件
        then
            ...执行语句
        else
            ...执行语句
        fi

【流控制语句】

    -a：逻辑与，仅当两个条件都成立时，结果为真

    -o：逻辑或，两个条件中有一个成立，结果即为真

【循环for语句】

    for 变量 in 名字表
    do
        命令列表
    done

    举例：

    for DAY in Monday Tuesday Wednesday
    do
        echo "The day is：$DAY"
    done

    for FILE in `ls`
    do
        echo $FILE
    done

【循环select语句】

    select 变量 in 关键字
    do
        command 1
        ...
        command n
        break
    done

    和for的区别在于：select以交互的方式执行do和done之间的命令

    ???

【case...esac语句】

    case 变量 in

        字符串1)
            命令列表1
        ;;

        ......

        字符串n)
            命令列表n
        ;;

        *)
            命令列表

    esac

    举例：

    echo '*****************************'
    echo "Please select your operation:"
    echo "Press C to Copy"
    echo "Press D to Delete"
    echo "Press B to Backup"
    echo '*****************************'

    read op
    case $op in
        C)
            echo "your selection is Copy"
        ;;
        D)
            echo "your selection is Delete"
        ;;
        B)
            echo "your selection is Delete"
        ;;
        *)
            echo "invalid selection"
    esac

【while循环语句】

    while 条件
    do
        命令
    done

【until循环语句】

    until 条件
    do
        命令
    done

    until和while不同的是，until的条件返回值为假时才继续执行

【break和continue语句】

    break       跳出整个循环

    continue    跳过本次循环，进行下次循环

【shift指令】

    参数左移，每执行一次，参数列表左移一个位置，$#的值减1，移出去的参数不可再用，用于分别处理每个参数。
    
    while [ $# -gt 0 ]
    do
        sum=`expr $sum + $1`
        shift
    done
    echo $sum

【函数】

   定义函数： 

        函数名(){

            命令序列

        }

    调用函数：

        函数名 参数1 参数2 ...

        不带()

    函数参数：

        定义函数时，不需要标明形参，但调用函数时可以传递参数给函数，并在函数中用$1、$2...来引用

    函数变量：

        函数中的变量均为全局变量，没有局部变量

【exit语句】

    退出程序执行，并返回一个返回码，返回码为0表示正常退出，非0表示非正常退出：exit 0

【Shell脚本调试】

    sh -x script

        执行每一行脚本前，先将此行脚本输出到屏幕上

    sh -n script

        不执行脚本，只是检查语法，将返回所有语法错误

【普通用户执行脚本需要的权限】

    #sh dir/scriptfile

        对dir有rx权限

        对scriptfile有r权限

    #dir/scriptfile

        对dir有rx权限

        对scriptfile有rx权限
