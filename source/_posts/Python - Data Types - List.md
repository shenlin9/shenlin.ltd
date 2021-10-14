---
title: Python - Data Types - List
categories:
- Python
tags:
- Python
- Python List
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

多维列表
```python
>>> d = [[1,2,3], [4,5,6], [7,8,9]]
>>> e = [[i**2 for i in x] for x in d]
>>> e
[[1, 4, 9], [16, 25, 36], [49, 64, 81]]
```

使用列表推导扁平化多维列表
```python
>>> d = [[1,2,3], [4,5,6], [7,8,9]]
>>> e = [x for row in d for x in row]
>>> e
[1, 2, 3, 4, 5, 6, 7, 8, 9]
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

但是对于上面的二维列表， Python 只将子列表创建为引用，而不是创建为单独的对象，例
如，下面只想更改第一行的第三个元素，结果所有行的第三个元素都被修改了：
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

List 是可修改对象，允许通过多种方法改变其长度。

直接把 List 相加，这时原 List 都没改变，返回的是一个新 List 对象：
```python
>>> L1 = ['a', 'b']
>>> L2 = ['c', 'd']
>>> L3 = ['e', 'f']
>>> L1 + L2 + L3
['a', 'b', 'c', 'd', 'e', 'f']
```

使用 extend 方法，这时是直接修改的原 List 对象：
```python
>>> L1 = ['a', 'b']
>>> L2 = ['c', 'd']
>>> L3 = ['e', 'f']
>>> L1.extend(L2)
>>> L1.extend(L3)
>>> L1
['a', 'b', 'c', 'd', 'e', 'f']
```

使用 append 方法，也是直接修改的原 List 对象，但是为原对象添加嵌套 List：
```python
>>> L1 = ['a', 'b']
>>> L2 = ['c', 'd']
>>> L3 = ['e', 'f']
>>> L1.append(L2)
>>> L1.append(L3)
>>> L1
['a', 'b', ['c', 'd'], ['e', 'f']]
```

## Access/Index

使用索引操作符 `[]`

索引必须是整数或切片，如 `[3]` `[3:5]`，使用其他类型数据作索引会导致 TypeError

索引从 0 开始，负数索引从 -1 开始，索引越界会导致 IndexError

## Slicing

切片操作语法：

`[start(optional):stop(optional):step(optional)]`

表示从索引 `start` 开始提取，到索引 `stop` 结束，不包括索引 `stop`，左含右不含，
即包含左边的元素，不包含右边的元素，这样也意味着 `stop` 是几就表示截取到第几个元
素（包含）。

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

>>> s = [0, 1, 2, 3, 4, 5, 6, 7, 8]
>>> s[0:6:2]
[0, 2, 4]

>>> s[2:8:-1]
[]
>>> s[8:2:-1]
[8, 7, 6, 5, 4, 3]
```

### 切片的应用场景

通过切片反转列表：
```python
>>> s = [0, 1, 2, 3, 4, 5, 6, 7, 8]
>>> s[::-1]
[8, 7, 6, 5, 4, 3, 2, 1, 0]
```

通过切片反转列表，且只保留第 1、3、5、7等奇数位的值：
```python
>>> s = [0, 1, 2, 3, 4, 5, 6, 7, 8]
>>> s[::-2]
[8, 6, 4, 2, 0]
```

创建列表的浅拷贝（Shadow Copy）：
```python
>>> s = [0, 1, 2, 3, 4, 5, 6, 7, 8]
>>> id(s)
2295131142984

>>> s2 = s[::]
>>> id(s2)
2295132095496
```

## Iterate

使用 for 循环对列表进行迭代：
```python
for element in List:
    print(element)
```

如果同时需要索引，搭配使用 enumerate：
```python
for index, element in enumerate(List):
    print(index. element)
```

如果只需要索引，搭配使用 len 和 range：
```python
for index in range(len(List)):
    print(index)
```

使用 len 和 range 也可以通过下标同时获取到索引和值，但不如 enumerate 简洁，且
enumerate 把各种迭代器包装为了生成器，一次只产生一对值，即索引和序列元素：
```python
>>> lst = [2, 3, 4, 5]

>>> for i in lst:
...     i
...
2
3
4
5

>>> for i in range(len(lst)):
...     print(i, '---', lst[i])
...
0 --- 2
1 --- 3
2 --- 4
3 --- 5

>>> for i,v in enumerate(lst):
...     print(i, '---', v)
...
0 --- 2
1 --- 3
2 --- 4
3 --- 5
```

enumerate 可以指定下标开始计数的值：

```python
>>> lst = [2, 3, 4, 5]
>>> for i,v in enumerate(lst, 1):
...     print(i, '---', v)
...
1 --- 2
2 --- 3
3 --- 4
4 --- 5
```

下面是循环时直接完成元组赋值
```python
>>> ls = [('a', 123, False),('b', 345, True),('c', 456, [8,9,11])]
>>> for v1,v2,v3 in ls:
...     print(v1, v2, v3)
...
a 123 False
b 345 True
c 456 [8, 9, 11]
```

列表支持迭代器协议，要想创建一个迭代器，可以使用内置的 iter 函数:
```python
it = iter(List)
element = it.next() # 第一个元素
element = it.next() # 第二个元素
```

## Add/Update elements

### 使用赋值操作符

可以一次性为列表的一个范围的切片进行赋值：
```python
>>> s = [0, 1, 2, 3, 4, 5, 6, 7, 8]
>>> s[2:5] = ['a', 'b', 'c']
>>> s
[0, 1, 'a', 'b', 'c', 5, 6, 7, 8]
```

### 使用 insert 方法

使用 insert 方法插入一个元素：
```python
>>> s = [0, 1, 2]
>>> s.insert(1, 'a')
>>> s
[0, 'a', 1, 2]
```

插入多个元素可以通过切片，为切片位置赋值：
```python
>>> s = [0, 1, 2]

>>> s[1:1]                  // 这样切片取不到元素，但可以插入元素
[]

>>> s[1:1] = ['a', 'b']
>>> s
[0, 'a', 'b', 1, 2]

>>> s[1:3] = ['c', 'd']
>>> s
[0, 'c', 'd', 1, 2]
```

## Remove/Delete elements

### 使用 del 关键字

使用 del 关键字可以移除一个元素、多个元素，甚至整个列表：
```python
>>> s = [0, 1, 2, 3, 4, 5, 6, 7, 8]

>>> del s[2]
>>> s
[0, 1, 3, 4, 5, 6, 7, 8]

>>> del s[3:5]
>>> s
[0, 1, 3, 6, 7, 8]

>>> del s
>>> s
NameError: name 's' is not defined
```

### 使用 remove, pop, clear 方法

remove 根据值删除元素
```python
>>> s = ['a', 'b', 'c']
>>> s.remove('b')
>>> s
['a', 'c']
```

pop 使用索引删除最末尾元素，且返回此元素值，这也是把列表当作堆栈使用的方法，遵循
FILO（First In Last Out） 模式：
```python
>>> s = ['a', 'b', 'c', 'd', 'e']
>>> s.pop()
'e'
>>> s
['a', 'b', 'c', 'd']
>>> s.pop()
'd'
>>> s
['a', 'b', 'c']
```

pop 也可以删除指定索引的元素：
```python
>>> s = ['a', 'b', 'c']
>>> s.pop(1)
'b'
>>> s
['a', 'c']
```

通过切片赋值来删除元素：
```python
>>> s = ['a', 'b', 'c', 'd', 'e']
>>> s[2:4] = []
>>> s
['a', 'b', 'e']
```

使用 clear 清空列表元素：
```python
>>> s = ['a', 'b', 'c', 'd', 'e']
>>> s.clear()
>>> s
[]
```

## Searching elements

使用 in 操作符检测元素是否在列表中：
```python
>>> 'a' in ['b', 'c', 'a']
True
>>> 'd' in ['b', 'c', 'a']
False
```

使用 index 方法找出符合其值的第一个索引：
```python
>>> ['b', 'c', 'a', 'c'].index('c')
1
```

index 方法执行一个线性搜索，找到第一个匹配时则中断搜索，最终没找到则抛出异常
ValueError：
```python
>>> ['b', 'c', 'a', 'c'].index('d')
ValueError: 'd' is not in list

>>> try:
...     result = ['b', 'c', 'a', 'c'].index('d')
... except ValueError:
...     result = 'no found'
...
>>> result
'no found'
```

index 可以指定第二个参数，表示开始搜索的索引位置：
```python
>>> ['a', 'b', 'c', 'b', 'e', 'b'].index('b')
1
>>> ['a', 'b', 'c', 'b', 'e', 'b'].index('b', 2)
3
>>> ['a', 'b', 'c', 'b', 'e', 'b'].index('b', 3)
3
```

???利用 index 的第二个参数自动跳出循环：
搜索所有匹配：
```python
>>> s = ['a', 'b', 'c', 'b', 'e', 'b']
>>> loc = -1
>>> try:
...     while 1:
...             loc = s.index('b', loc+1)
...             print("match at:", loc)
... except ValueError:
...     pass
...
match at: 1
match at: 3
match at: 5
```

min 和 max 方法支持可迭代对象，因此可用于列表，找出列表元素的最小值和最大值：
```python
>>> min([1, 2, 3, 4])
1
>>> max([1, 2, 3, 4])
4
```

## Sorting

列表排序可使用列表自己实现的 sort 方法，或者使用 python 内置的 sorted 函数。

列表的 sort 方法，默认为升序，若改为降序，需传参数 `reverse = True`：
```python
>>> s = ['b', 'd', 'c', 'a']
>>> s.sort()
>>> s
['a', 'b', 'c', 'd']

>>> s.sort(reverse = True)
>>> s
['d', 'c', 'b', 'a']
```

内置的 sorted 函数，返回的是一个新列表，默认也是升序，改为降序，则第二个参数传
`reverse = True`：
```python
>>> s = ['b', 'd', 'c', 'a']
>>> s2 = sorted(s)
>>> s2
['a', 'b', 'c', 'd']
>>> s3 = sorted(s, reverse = True)
>>> s3
['d', 'c', 'b', 'a']
```

Please note that **in place** sorting algorithms are more efficient as they don
’t need temporary variables (such as a new list) to hold the result.
原地排序算法更有效，因为不需要临时变量(如新列表)来保存结果。

## list methods

append()	It adds a new element to the end of the list.
extend()	It extends a list by adding elements from another list.
insert()	It injects a new element at the desired index.
remove()	It deletes the desired element from the list.
pop()	    It removes as well as returns an item from the given position.
clear()	    It flushes out all elements of a list.
index()	    It returns the index of an element that matches first.
count()	    It returns the total no. of elements passed as an argument.
sort()	    It orders the elements of a list in an ascending manner.
reverse()	It inverts the order of the elements in a list.
copy()	    It performs a shallow copy of the list and returns.

## list built-in functions

all()	It returns True if the list has elements with a True value or is blank.
any()	If any of the members has a True value, then it also returns True.
enumerate()	It returns a tuple with an index and value of all the list elements.
sorted()	It returns the sorted copy of the list.
len()	The return value is the size of the list.
list()	It converts all iterable objects and returns as a list.

max()	The member having the maximum value
min()	The member having the minimum value
sum()	The return value is the aggregate of all elements of a list.


