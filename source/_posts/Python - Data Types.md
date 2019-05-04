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

参考

## Strings

参考

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

参考

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
