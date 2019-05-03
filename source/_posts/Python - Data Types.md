---
title: Python - Data Types
categories:
- Python
tags:
- Python
- Python Data Types
date: 2019-04-30 16:27:34
---

Python - Data Types

<!--more-->

## Booleans

True    False

## Numbers

int, float, complex

### int

Python 中的 int 没有大小限制，其只受可用内存的限制：
```python
>>> num = 798234792641618274123719283712301928301928301928309128312093234324234
>>> num.bit_length()
229

>>> num
798234792641618274123719283712301928301928301928309128312093234324234
```

### float

Python 中 float 的精度可以到 15 位小数：
```python
>>> import sys

>>> sys.float_info
sys.float_info(max=1.7976931348623157e+308, max_exp=1024, max_10_exp=308, min=2.2250738585072014e-308, min_exp=-1021, min_10_exp=-307, dig=15, mant_dig=53, epsilon=2.220446049250313e-16, radix=2, rounds=1)

>>> sys.float_info.dig      # 最大小数位数
15
```

??? 小数位数可以超过 15 位
```python
>>> f = 0.12345678901234567890123128723479823479234928349234
>>> type(f)
<class 'float'>
```

??? 小数比较时有效位到 17 位
```python
>>> f = 0.12345678901234567
>>> g = 0.12345678901234568
>>> print(g > f)
True

>>> f = 0.123456789012345678
>>> g = 0.123456789012345679
>>> print(g > f)
False
```

### complex

除了常见的 int 和 float，Python 还引入了另一种数字类型：complex

在数字后添加字母 `j` 或 `J`，可以使它成为虚数或复数：
```python
>>> type(8)
<class 'int'>
>>> type(8j)
<class 'complex'>
>>> type(8J)
<class 'complex'>
>>> type(8.1J)
<class 'complex'>
```

使用内置函数 `type()` 确定值或变量的数据类型，使用内置函数 `isinstance()` 测试对
象类型：
```python
>>> i = 10
>>> type(i)
<class 'int'>
>>> isinstance(i, int)
True

>>> f = 10.0
>>> type(f)
<class 'float'>
>>> isinstance(f, float)
True

>>> m = 10j
>>> type(m)
<class 'complex'>
>>> isinstance(m, complex)
True
```

可以使用 complex 类型作为构造函数来形成一个复数：
```python
>>> complex(3, 5)
(3+5j)

>>> type(complex(3, 5))
<class 'complex'>
```

## Strings

Python 中使用单引号、双引号、三个单引号或三个双引号包含起来的一个或多个字符序列
称为字符串。

Python 中单引号、双引号的作用一样：
```python
>>> str = 'abc\ndef'
>>> str
'abc\ndef'

>>> str = "abc\ndef"
>>> str
'abc\ndef'
```

Python 使用三个引号支持换行字符串：
```python
>>> str = '''
... abc
... def
... '''
>>> str
'\nabc\ndef\n'

>>> str = """
... abc
... def
... """
>>> str
'\nabc\ndef\n'
```

可以在单引号中使用双引号，双引号中使用单引号，三个引号的也适用此规则。

### Immutable

Python 中的字符串是不可变的，这表示将分配一次内存并在此后重复使用：
```python
>>> str = 'abc'
>>> str2 = str
>>> id(str)
2189979736024
>>> id(str2)
2189979736024
```

若要对字符串删除字符、修改字符等任何修改都会导致错误：
```python
>>> str = 'i am super man'
>>> str[0] = 'I'
TypeError: 'str' object does not support item assignment

>>> del str[0]
TypeError: 'str' object doesn't support item deletion
```

只可以移除整个字符串：
```python
>>> str1 = 'hello, world!'
>>> del str1
>>> print(str1)
NameError: name 'str1' is not defined
```

### Unicode

Python 的两个流行版本：2.7 和 3.4           （??? 3.4 需确认）

Python 2 中的字符串默认是 8 位 ASCII 编码，字母 'u' 作前缀表示 Unicode 编码字符
串：
```python
>>> type("python string in Python 2")
<type 'str'>

>>> type(u"python string in Python 2")          # 字符串前加 u 表示 Unicode
<type 'unicode'>
```

Python 3 中的字符串默认就是 Unicode 的 UTF-8 编码：
```python
>>> type("python string in Python 3")
<class 'str'>

>>> type(u"python string in Python 3")
<class 'str'>
```

### String Operators

#### Concatenation +

使用 `+` 号连接字符串：
```python
>>> "this" + "world"
thisworld
```

### Repetition *

使用 `*` 号重复字符串：
```python
>>> str1 = 'xyz'
>>> str1 * 3
'xyzxyzxyz'

>>> 'a' * 3
'aaa'
```

#### Range []

使用 `[]` 提取单个字符：
```python
>>> "hello"[1]
'e'

>>> "hello"[-1]
'o'
```

字符串的字符下标从 0 开始，但负数下标，则是从 -1 开始，即：

    P   Y   T   H   O   N
    0   1   2   3   4   5
    -6  -5  -4  -3  -2  -1

#### Range Slicing [x:y]

使用 `[x:y]` 语法从字符串中提取子字符串：
```python
>>> str = "0123456789"

>>> print(str[1:4])
123
>>> print(str[4:])
456789
>>> print(str[:4])
0123

>>> print(str[-3:])
789
>>> print(str[:-3])
0123456
>>> print(str[-3:-1])
78

>>> print(str[:])
0123456789
```

#### Membership (in & not in)

in 和 not in 判断是否包含子字符串：
```python
>>> 'he' in 'hello'
True

>>> 'he' not in 'hello'
False
```

#### Interating (for)

使用 for 逐个字符迭代一个字符串：
```python
>>> for s in 'hello':
...     s
...
'h'
'e'
'l'
'l'
'o'
```

#### Raw String (r/R)

字符串前加 `r` 或 `R` 表示原生字符串，会忽略字符串中转义字符的含义：
```python
>>> 'a\nb'
'a\nb'
>>> print("a\nb")
a
b

>>> print(r"a\nb")
a\nb
>>> print(R"a\nb")
a\nb
```

### String Functions



## Bytes

Python 中的 byte 是可不变类型，可以通过索引读取字节，但不能修改其值。
```python
>>> type(bytes(4))
<class 'bytes'>

>>> print(bytes(4))
b'\x00\x00\x00\x00'

>>> b = bytes(3)
>>> b[1]
0
>>> b[1] = 1
TypeError: 'bytes' object does not support item assignment
```

Byte 和 String 区别：
* Byte 对象包含字节序列，而 String 对象包含字符序列
* Byte 对象是机器可读的对象，而 String 对象是人类可读的形式
* Byte 对象机器可读，则可以直接写入磁盘，而 String 对象在写入磁盘前需要先编码

对于带缓存的 I/O 操作，Byte 对象很重要，例如一个程序连续通过网络接收数据，在消息
头和终止符出现在流中时解析数据，并把不断传入的字节追加到缓冲区，使用 Python 的
Byte 对象则很容易实现上，伪代码如下：
```python
buf = b''
while message_not_complete(buf):
    buf += read_from_socket()
```

## Lists

List 是一个类似数组的结构，它以有序序列的形式存储任意类型的对象。

它是各种数据类型项的混合集合，值可以重复。

它非常灵活，没有固定的尺寸，索引以 0 开头。

### Syntax

创建 List，直接把元素使用逗号分隔放入中括号：
```python
>>> my_list = [3, 3.1, 3.1j, True, 'Python', b'Hello', 3]
>>> for s in my_list:
...     print(s)
...
3
3.1
3.1j
True
Python
b'Hello'
3
```

List 可嵌套：
```python
>>> my_list = [['a', 'b'], [1, 2], [True, False]]
>>> for m in my_list:
...     for n in m:
...             print(n)
...
a
b
1
2
True
False
```

### Slicing

切片操作符冒号 `:`，语法格式为 `[m:n]`，表示从索引 `m` 开始提取，到索引 `n-1` 结
束，不包括索引 `n`，注意切片操作都是左含右不含，即包含左边的元素，不包含右边的元
素。

可以像字符串一样，通过切片操作符提取元素：
```python
>>> my_list = [0, 1, 2, 3, 4, 5, 6, 7]
>>> my_list[3:6]
[3, 4, 5]
>>> my_list[3:]
[3, 4, 5, 6, 7]
>>> my_list[:3]
[0, 1, 2]

>>> my_list[:-2]
[0, 1, 2, 3, 4, 5]
>>> my_list[-2:]
[6, 7]
>>> my_list[-4:-2]
[4, 5]

>>> my_list[:]
[0, 1, 2, 3, 4, 5, 6, 7]
```

## Tuples

Tuples 也是各种数据类型项的混合集合，值可以重复。

创建 List 使用中括号，创建 Tuple 使用小括号，空 Tuple：
```python
>>> empty_tuple = ()
>>> type(empty_tuple)
<class 'tuple'>
>>> print(empty_tuple)
()
```

嵌套 Tuple：
```python
>>> first_tuple = (0, 1, 2)
>>> second_tuple = ('a', 'b', 'c')
>>> my_tuple = (first_tuple, second_tuple)
>>> print(my_tuple)
((0, 1, 2), ('a', 'b', 'c'))
```

重复 Tuple：
```python
>>> my_tuple = ('a', 'b', 'c') * 2
>>> print(my_tuple)
('a', 'b', 'c', 'a', 'b', 'c')
```

切片 Tuple：
```python
>>> my_tuple = (0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
>>> my_tuple[2:5]
(2, 3, 4)
>>> my_tuple[2:]
(2, 3, 4, 5, 6, 7, 8, 9)
>>> my_tuple[:5]
(0, 1, 2, 3, 4)
>>> my_tuple[-4:-2]
(6, 7)
>>> my_tuple[-4:]
(6, 7, 8, 9)
>>> my_tuple[:-4]
(0, 1, 2, 3, 4, 5)
>>> my_tuple[:]
(0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
```

Tuple 和 List 的共性如下：
* 都是有序序列
* 都支持索引和重复项
* 都可以存储不同类型数据
* 都支持嵌套

Tuple 不同于 List 的地方：
* List 是可变的，Tuple 是不可变的，在创建 Tuple 之后不允许增加、删除元素。

虽然 Tuple 创建后不可以更改，但若 Tuple 元素本身是可变的，则可更改其元素：
```python
>>> my_tuple = (0, 1, 2, ['a', 'b', 'c'], 3, 4)
>>> print(my_tuple)
(0, 1, 2, ['a', 'b', 'c'], 3, 4)

>>> my_tuple[3][0] = 'A'
>>> print(my_tuple)
(0, 1, 2, ['A', 'b', 'c'], 3, 4)
```

为什么有了 List 还需要 Tuple 数据类型：
* Python 使用 Tuple 类型来从一个函数返回多个值
* Tuple 比 List 轻量
* Tuple 就像一个容器，可以装很多东西
* 可以使用 Tuple 的元素作为字典的键

## Sets

Set 数据类型支持数学运算，例如并集、交集、对称差分等。

Set 数据类型特点：
* 元素无序
* 元素需唯一不可重复（因此 Set 数据类型由数学的集合派生其实现）
* 元素均为不可变对象
* 以大括号包含元素，元素之间逗号分隔

为什么需要 Set 数据类型：
当检查是否包含特定的元素时，Set 类型比 List 具有明显的优势，因为 Set 实现了一个
基于哈希表的高度优化的方法。

创建 Set 数据类型：
```python
>>> my_set = {'abc', True, 123, "abc"}
>>> print(my_set)
{True, 123, 'abc'}
```

还可以使用内置 set 函数，注意是每个字符一个元素：
```python
>>> my_set = set("abc")
>>> print(my_set)
{'b', 'a', 'c'}
```

其实 Tuple 和 List 都有类似的内置函数：
```python
>>> my_tuple=tuple("asdf")
>>> print(my_tuple)
('a', 's', 'd', 'f')

>>> my_list = list("123")
>>> print(my_list)
['1', '2', '3']
```

### Frozen Set

Frozen Set 是普通 Set 的一种处理形式，是不可改变的，只支持那些不改变 Frozen Set
的操作和方法，如标准 Set 可以通过 add 添加元素，而 Frozen Set 则不可：
```python
>>> a = {1, 2, 3}
>>> b = frozenset(a)
>>> a.add(4)
>>> print(a)
{1, 2, 3, 4}
>>> print(b)
frozenset({1, 2, 3})
```

## Dictionaries

Python 中的 Dictionary 特点：
* 是 Python 内置的映射类型，使用键值对，提供了一种直观的数据存储方式
* 无序集合

为什么需要 Dictionary?
Dictionary 解决了高效存储大数据集的问题，Python 对 Dictionary 对象进行了高度优化
，使其更适合于检索数据。

创建 Dict 和 Set 一样，也使用大括号语法和逗号分隔元素，不过元素是键值对：
```python
>>> dict = {'a':1, 'b':2, 'c':3}
>>> print(dict)
{'a': 1, 'b': 2, 'c': 3}
>>> print(dict['b'])
2
>>> dict['c'] = 33
```

???键也可为数字：
```python
>>> my_dict = {1:123, 2:234}
>>> type(my_dict)
<class 'dict'>
>>> print(my_dict)
{1: 123, 2: 234}
>>> my_dict[1]
123
>>> my_dict[2]
234
```

内置的 Dict 的方法：
* keys() 从字典提取所有键，返回类型是 dict_keys
* values() 从字典提取所有值，返回类型是 dict_values
* items() 从字典提取所有键值对返回，返回类型是 dict_items
```python
>>> dict = {'a':1, 'b':2, 'c':3}
>>> dict.keys()
dict_keys(['a', 'b', 'c'])
>>> dict.values()
dict_values([1, 2, 3])
>>> dict.items()
dict_items([('a', 1), ('b', 2), ('c', 3)])
```

注意其返回类型不是 List，不可以通过索引的方式取值，但可以循环：
```python
>>> type(dict.keys())
<class 'dict_keys'>
>>> type(dict.values())
<class 'dict_values'>
>>> type(dict.items())
<class 'dict_items'>

>>> print(dict.keys()[0])
TypeError: 'dict_keys' object does not support indexing

>>> for s in dict.keys():
...     print(s)
...
a
b
c
>>> for s in dict.values():
...     print(s)
...
1
2
3
>>> for s in dict.items():
...     for a in s:
...             print(a)
...
a
1
b
2
c
3
```

从 Dict 添加、修改、删除元素：
```python
>>> dict = {'a': 1, 'b': 2, 'c': 3}

>>> dict['d'] = 4       # 添加元素方式一：直接为不存在的键赋值
>>> dict
{'a': 1, 'b': 2, 'c': 3, 'd': 4}

>>> dict.update({'e': 5})   # 添加元素方式二：使用 update 方法
>>> dict
{'a': 1, 'b': 2, 'c': 3, 'd': 4, 'e': 5}

>>> del dict['e']
>>> dict
{'a': 1, 'b': 2, 'c': 3, 'd': 4}
```
