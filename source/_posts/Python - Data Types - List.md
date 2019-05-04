---
title: Python - Data Types - List
categories:
- Python
tags:
- Python
- List
date: 2019-05-04 15:07:03
---

Python - Data Types - List

<!--more-->

List 是一个类似数组的结构，它以有序序列的形式存储任意类型的对象。

它是各种数据类型项的混合集合，值可以重复。

它非常灵活，没有固定的尺寸，索引以 0 开头。


## Create

### [ ]

创建 List，直接把元素使用逗号分隔放入中括号：
```python
>>> empty_list = []
>>> type(empty_list)
<class 'list'>

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

### list() method

内置方法 list() 可接受可迭代对象为参数，所以此方法主要作用应该是把各种可迭代对象
转换为 List 对象：
```python
>>> s = list()
>>> type(s)
<class 'list'>
>>> print(s)
[]

>>> s = list((1, 2, 3))
>>> type(s)
<class 'list'>
>>> print(s)
[1, 2, 3]

>>> s = list([1, 2, 3])
>>> type(s)
<class 'list'>
>>> print(s)
[1, 2, 3]

>>> s = list({1, 2, 3})
>>> type(s)
<class 'list'>
>>> print(s)
[1, 2, 3]

>>> s = list({'a':1, 'b':2, 'c':3})
>>> type(s)
<class 'list'>
>>> print(s)
['a', 'b', 'c']

>>> s = list([1, 2, ['a', 'b'], 3, 4])
>>> print(s)
[1, 2, ['a', 'b'], 3, 4]
```

### List Comprehension

Python 支持一个概念称为“列表推导”，以一种完全自然和简单的方式构建列表。

语法格式:
`theList = [expression(iter) for iter in oldList if filter(iter)]`

例如
```python
>>> my_list = [ite + 1 for ite in range(5)]
>>> print(my_list)
[1, 2, 3, 4, 5]
```

使用过滤条件：
```python
>>> print([x+y for x in 'abc' for y in '123'])
['a1', 'a2', 'a3', 'b1', 'b2', 'b3', 'c1', 'c2', 'c3']

>>> print([x+y for x in 'abc' for y in '123' if x != 'a' and y != '1'])
['b2', 'b3', 'c2', 'c3']
```

例子：奇数月列表
```python
>>> months = ['jan', 'feb', 'mar', 'apr', 'may', 'jun', 'jul', 'aug', 'sep', 'oct', 'nov', 'dec']
>>> odd_months = [m for index, m  in enumerate(months) if index % 2 == 0]
>>> print(odd_months)
['jan', 'mar', 'may', 'jul', 'sep', 'nov']
```

### multi-dimensional list

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

通过为每个元素指定一个初始值，可以创建具有预定义大小的序列：
```python
>>> init_list1 = [0] * 3
>>> print(init_list1)
[0, 0, 0]
```

按照上面的概念创建二维列表：
```python
>>> init_list2 = [[0] * 3] * 3
>>> print(init_list2)
[[0, 0, 0], [0, 0, 0], [0, 0, 0]]
```

但是对于上面的二维列表， Python 只将引用创建为子列表，而不是创建单独的对象，例如
，下面只想更改第一行的第三个元素，结果所有行的第三个元素都被修改了：
```python
>>> init_list2[0][2] = 2
>>> print(init_list2)
[[0, 0, 2], [0, 0, 2], [0, 0, 2]]
```

可以使用列表推导来解决上述二维列表的问题：
```python
>>> init_list2 = [[0] * 3 for i in range(3)]
>>> print(init_list2)
[[0, 0, 0], [0, 0, 0], [0, 0, 0]]
>>> init_list2[0][2] = 2
>>> print(init_list2)
[[0, 0, 2], [0, 0, 0], [0, 0, 0]]
```

### extend(), append()

## Access/Index
2.1 Using the index operator
2.2 Access a List using Index – Example
2.3 Reverse indexing

## Slicing
3.1 Slicing Examples
3.1.1 Return the three elements, i.e. [3, 4, 5] from the list
3.1.2 Print slice as [3, 5], Don’t change the first or last index
3.1.3 Slice from the third index to the second last element
3.1.4 Get the slice from start to the second index
3.1.5 Slice from the second index to the end
3.1.6 Reverse a list using the slice operator
3.1.7 Reverse a list but leaving values at odd indices
3.1.8 Create a shallow copy of the full list
3.1.9 Copy of the list containing every other element

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

## Iterate
4.1 Traversing a List – Example

## Add/Update elements
5.1 Modifying a List using Assignment Operator
5.2 Modifying a List with Insert Method

## Remove/Delete elements
6.1 Delete List Elements using Del Operator
6.2 Delete List Elements using Remove() and POP()

## Searching elements
7.1 Traversing a list in Python – Example

## Sorting
8.1 List’s sort() method
8.2 Built-in sorted() function

## list methods


## list built-in functions


