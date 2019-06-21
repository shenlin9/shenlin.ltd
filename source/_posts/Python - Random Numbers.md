---
title: Python - Random Numbers
categories:
- Python
tags:
- Python
- Random Numbers
date: 2019-05-29 14:55:57
---

Python - Random Numbers

<!--more-->

## PRNG

Python 有一系列随机数函数：randint(), random(), choice(), uniform()

在核心部分，Python 使用梅森旋转算法(Mersenne Twister Algorithm)，一个伪随机数
生成器(PRNG：pseudo-random generator)来生成伪随机数。

伪随机数生成器 PRNG：pseudo-random generator

研究表明，PRNGs 适用于模拟和建模等应用，但不推荐用于密码目的。同样的规则也适用于
Python 随机数生成器。

我们可以将其用于编程任务，比如生成指定范围内的随机整数、从列表中随机选择项或随机
打乱序列。

现在让我们看看如何使用 Python 随机模块，并查看生成随机数的不同函数。

## random.random()

默认情况下，random()函数生成0.0到1.0之间的浮点数。
```python
>>> import random
>>> random.random()
0.22766470533291527
>>> random.random()
0.8688532316849745
```

random() 函数还是 random 模块中大多数方法的先决条件之一。

## 随机数三类

可以将随机数生成分为三类，每个类别都有一些随机函数。

### 生成指定范围内的一个随机数

**randrange**

```python
r = Randrange([Start,] Stop [, Step])
```

产生的随机数 r 范围： `Start <= r < Stop`
Start 默认值 0
Step 默认值 0

只有一个参数表示 stop，而 start，step 都为 0：
```python
>>> random.randrange(8)
1
>>> random.randrange(8)
5
>>> random.randrange(8)
7
```

生成 1 到 10 之间的随机奇数
```python
>>> import random
>>> random.randrange(1,10,2)
3
>>> random.randrange(1,10,2)
5
>>> random.randrange(1,10,2)
7
>>> random.randrange(1,10,2)
1
```

生成 1 到 10 之间的随机偶数
```python
>>> random.randrange(2,10,2)
4
>>> random.randrange(2,10,2)
6
>>> random.randrange(2,10,2)
2
```

**randint**

```python
r = random.randint(start, stop)
```
产生的随机数 r 范围： `Start <= r <= Stop`，即两个节点都包含

```python
>>> random.randint(2,4)
4
>>> random.randint(2,4)
3
>>> random.randint(2,4)
2
```

### 从序列中随机选择一个值

**Random.Choice(Seq)**

函数的作用是:从给定序列中任意确定一个元素。

注意:Python 中的序列是有序集合的通称，如列表、元组等。
```python
>>> random.choice(['a','b','c','d'])
'b'
>>> random.choice(['a','b','c','d'])
'c'
>>> random.choice(['a','b','c','d'])
'd'

>>> random.choice('this is a string')
't'
>>> random.choice('this is a string')
'n'
>>> random.choice('this is a string')
' '
>>> random.choice('this is a string')
'h'
```

**Random.Shuffle(List)**

函数的作用是:重新排列列表中的项，使它们以随机顺序出现。

采用了复杂度为 `O(n)` 的洗牌算法(Fisher-Yates Algorithm)。

它首先将数组中的最后一个元素迭代到第一个条目，然后用随机索引值交换每个条目。

```python
>>> a = ['a','b','c','d']

>>> random.shuffle(a)
>>> a
['c', 'd', 'b', 'a']

>>> random.shuffle(a)
>>> a
['a', 'c', 'd', 'b']

>>> random.shuffle(a)
>>> a
['b', 'd', 'a', 'c']
```

**Random.Sample(Collection, Random List Length)**

从给定集合(list、tuple、string、dictionary、set)中随机选择 N 项，并将它们作为列
表返回。

它的工作原理是采样而不替换。这意味着序列中的单个元素最多可以出现在结果列表中一次。

```python
>>> random.sample({'a','b','c','d'}, 3)
['c', 'd', 'a']
>>> random.sample({'a','b','c','d'}, 3)
['b', 'd', 'a']

>>> random.sample({'a','b','c','d'}, 2)
['b', 'c']
>>> random.sample({'a','b','c','d'}, 2)
['b', 'a']
```

### 生成一个随机浮点数

**Random.Random()**

生成随机浮点数，其范围 `0.0 <= r < 1.0`

```python
>>> random.random()
0.22560807392642934
>>> random.random()
0.050008471469260796
>>> random.random()
0.671316639060644
```

**Random.Seed()**

用于设置种子值作为基数来产生随机数，对伪随机数生成器(PRNG)进行初始化。
如果不提供种子值，Python 默认使用系统当前时间作为种子值。

设置同样的种子值，则其后续生成的随机数是一样的：
```python
>>> random.seed(5)
>>> random.random()
0.6229016948897019
>>> random.random()
0.7417869892607294
>>> random.random()
0.7951935655656966

>>> random.seed(5)
>>> random.random()
0.6229016948897019
>>> random.random()
0.7417869892607294
>>> random.random()
0.7951935655656966
```

**Random.Uniform(Lower, Upper)**

生成浮点随机数

它是 random.random() 函数的扩展，可以指定随机数的上界和下界，而不是只能是 0 到 1
之间：
```python
>>> random.uniform(3.3, 33.3)
19.47974403163848
>>> random.uniform(3.3, 33.3)
26.351301353221498
```

生成固定精度的浮点随机数：
```python
>>> round(random.uniform(3.3, 33.3), 2)
13.23
>>> round(random.uniform(3.3, 33.3), 2)
21.28
>>> round(random.uniform(3.3, 33.3), 2)
12.64
```

**Random.Triangular(Low, High, Mode)**

用于生成随机浮点数，结果范围 `Low <= r <= High`，结果基本是对称分布
Low 默认为 0
High 默认为 1
Mode 默认为两个值的中间值

可以不指定参数，则取默认值：
```python
>>> random.triangular()
0.9251849587448424
>>> random.triangular()
0.35983853318585096
```

```python
>>> random.triangular(2.5,10.5,3.5)
4.272631228683011
>>> random.triangular(2.5,10.5,3.5)
3.6613950473130767
```

## How To Generate A Secure Random Number?

PRNGs 在默认情况下是不安全的，Python中有三种方法可以生成安全的随机值:
* Python 3.6 Secrets Module
* SystemRandom() Class Random() Method
* Os.Urandom()

### Python 3.6 Secrets Module

首先，Python 3.6 引入了一个名为 Secrets 的模块，它定义了能够生成加密安全的随机输
出的函数。它们在下列条件下:
* secret 模块的方法可以生成字节序列而不是单个整数/浮点值。
* 输出质量将根据目标机器的操作系统而变化。 

```python
>>> from secrets import *
>>> token_bytes(16)
b'\x07m\x04\x04\xc9\xee\x019\x96!\x87\xfdUF\xfe\xc1'
```

注意，只有 Python 3.6 及其之后才有 Secrets 的模块

### SystemRandom() Class Random() Method

```python
>>> from random import random
>>> from secrets import *
>>> SystemRandom().random()
0.8339282323538677
>>> SystemRandom().random()
0.17151551361330708
```

### Os.Urandom()

urandom() 方法内部也是调用了 SystemRandom() 类
```python
>>> from os import *
>>> urandom(10)
b'.\x1f\xecg\x9986\xc7\xb1\r'
>>> urandom(5)
b'\x06\xd2e\xc8K'
>>> urandom(8)
b"\x96\xd9\\h\x89?'\xbb"
```

### Convert Random Bytes Into A Number

上面的两个方法产生的都是随机字节，而不是随机整数，可以用 binascii 模块把字节输出
转换为整型输出：

```python
>>> import os
>>> import binascii

>>> os.urandom(10)
b'\x13\xd3\xfb\x8c\x99\xbb\xb0(\xfa\r'

>>> binascii.hexlify(os.urandom(10))
b'08ba5649b3f799367794'

>>> binascii.hexlify(os.urandom(10))
b'8651a8a4b6a6a965f9f5'

>>> int(binascii.hexlify(os.urandom(10)), 16)
984764163805101149245832

>>> int(binascii.hexlify(os.urandom(10)), 16)
392335373328387210478599
```

## State Of Random Number Generator In Python

random 模块包括以下两个方法用来管理随机数生成器的状态。一旦状态可用，就可以使用
它来重新生成相同的随机值：
* Random.Getstate()
* Random.Setstate(State)

### Random.Getstate()

它返回一个对象，该对象携带随机数生成器的当前状态。您可以使用此对象调用setstate()
方法来在任何时间点恢复随机数生成器的状态。

注意:通过重置状态，可以强制生成器再次给出相同的随机数。应该只在需要时使用此功能。

```python
>>> a = random.getstate()
>>> a
(3, (2147483648,  503495101, 1780362348, 4008394298, 1976654408, 1223369939,
3096102077, 184765096, 3266956109, 624), None)
>>> random.setstate(a)
```

### Random.Setstate(State)

将生成器的当前状态重置为输入状态对象。

### How To Call Getstate()/Setstate()

如果通过保存的随机数生成器的状态来重置它，则可以重新生成相同的随机数。但是，如果
更改任意一个随机函数或它们的参数，则会破坏逻辑。

```python
import random

def main():

    a = ['a','b','c','d','e','f','g']
    print('before1:', random.sample(a, 3))
    print('before2:', random.sample(a, 3))
    print('before3:', random.sample(a, 3))

    s = random.getstate()
    print('1:', random.sample(a, 3))
    print('2:', random.sample(a, 3))
    print('3:', random.sample(a, 3))

    random.setstate(s)
    print('1:', random.sample(a, 3))
    print('2:', random.sample(a, 3))
    print('3:', random.sample(a, 3))

main()

# output

C:\Users\T460P>py test.py
before1: ['b', 'c', 'g']
before2: ['c', 'f', 'g']
before3: ['e', 'd', 'a']

1: ['e', 'g', 'c']
2: ['e', 'c', 'g']
3: ['a', 'd', 'e']

1: ['e', 'g', 'c']
2: ['e', 'c', 'g']
3: ['a', 'd', 'e']
```

## Using NumPy for random number arrays

NumPy 是 Python 中的一个科学计算模块。它提供了生成多维数组的函数。

因为这个模块在 Python 中默认是不可用的，所以你需要先运行它：
```python
pip install numpy
```

???numpy 中方法的调用方式
```python
>>> import random
>>> random.random_integers(1,10,2)
AttributeError: module 'random' has no attribute 'random_integers'

>>> import numpy
>>> random.random_integers(1,10,2)
AttributeError: module 'random' has no attribute 'random_integers'

>>> numpy.random_integers(1,10,2)
AttributeError: module 'numpy' has no attribute 'random_integers'
```

random_integers 已弃用，使用 randint 替代：
```python
>>> from numpy import *
>>> random.random_integers(1, 99, 7)
__main__:1: DeprecationWarning: This function is deprecated. Please call randint(1, 99 + 1) instead
array([95, 30, 91, 79, 18, 39, 31])
```

numpy 应该是覆盖了默认的 random.choice 方法：
```python
>>> random.choice(seq)
0.4
>>> random.choice(seq, 1)
TypeError: choice() takes 2 positional arguments but 3 were given
>>> random.choice(seq, size=2)
TypeError: choice() got an unexpected keyword argument 'size'

>>> from numpy import *
>>> random.choice(seq, size=2)
array([0.2, 0.3])
>>> random.choice(seq, size=2, replace=False)
array([0.1, 0.4])
>>> random.choice(seq, size=2, replace=True)
array([0.2, 0.2])
```

## How to generate a universal random number

### UUID

Universal Unique Identifier 通用唯一标识符

UUID 是分配给对象或实体用于惟一标识的 128 位长数字。根据规范，不太可能重新生成相
同的 UUID。

Python 有个 UUID 模块，可以生成不可变 UUID 对象。

Python UUID 模块中的所有函数都与 UUID 的可用版本兼容。

如果希望生成加密安全的随机 UUID，那么推荐使用 `uuid.uuid4()` 函数。

```python
>>> import uuid
>>> uuid.uuid4()
UUID('a4c3e907-2c1c-4974-81ea-ad488f61b86c')
>>> uuid.uuid4()
UUID('5121ecb2-516d-4a34-b5d1-64cea853509a')
```

Why Use UUID?
在 UUID 的帮助下，很容易定位数据库或计算机系统中的特定文档、用户、资源或任何信息。

## Exercise – Guess the number game using random module



