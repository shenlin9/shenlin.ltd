---
title: Python - Generator
categories:
- Python
tags:
- Python
- Generator
date: 2019-05-22 14:16:32
---

Python - Generator

<!--more-->

## Iterator and Generator

Python 中构建迭代器很麻烦：首先，我们需要编写一个类并实现 `__iter__()` 和
`__next__()` 方法。其次，我们需要管理内部状态，并在没有可返回的元素时抛出
`StopIteration` 异常。

Python 生成器提供了一种创建迭代器的简单方法，使得创建生成器的过程与编写常规函数
一样简单。

Python 中的生成器是一个具有独特功能的函数。我们可以在运行时暂停或恢复它。它返回
一个生成器对象，我们可以遍历该对象并在每次迭代中访问一个值。

## Create Generators

有两种简单的创建生成器的方法

### Return and Yield

return 是函数的最终语句。它提供了一种将一些值返回的方法。返回时，它的本地堆栈也
被清空，任何对函数的新调用都将从第一个语句开始执行。

yield 正相反，它保存了随后的函数调用之间的状态。它从将控制权交给调用者的位置恢复
执行，也就是，最后一个 yield 语句之后。

### Generator Function

像创建一个自定义函数一样，唯一不同，是使用 yield 语句代替 return 语句，这样会告
诉 Python 解释器，这个函数是一个生成器，返回的是一个迭代器，语法：
```python
def gen_func(args):
    ...
    while [cond]:
        ...
        yield [value]
```
return 语句是普通函数中的最后一个调用，而这里的 yield 语句则会临时挂起函数，保存
状态，然后稍后继续执行。

```python
>>> def fibonacci(num):
...     x1 = 0
...     x2 = 1= 0
...     x2 = 1
...     count = 0
...     while count < num:
...             xth = x1 + x2
...             x1 = x2
...             x2 = xth
...             count += 1
...             yield xth
...
>>> fibonacci(5)
<generator object fibonacci at 0x000001D8E7908A20>
...
>>> fib = fibonacci(5)
>>> next(fib)
1
>>> next(fib)
2
>>> next(fib)
3
>>> next(fib)
5
>>> next(fib)
8
>>> next(fib)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

定义好生成器函数，然后调用生成器函数后，其仅仅返回一个生成器对象(生成器对象只是
一个保存了生成器状态的标识符)，并不执行生成器函数代码，只有我们对返回的生成器对
象调用 next() 方法时，其代码才会执行，也因此很节省内存。

next() 方法是我们可以从生成器函数请求值的最常见方法，对生成器对象的每次 next()
调用都会导致生成器函数执行，直到找到 yield 语句为止(一个生成器函数可以有多个
yield 语句)。然后，Python 将生成的值返回给调用者，并保留生成器的状态以供将来使用
。
```python
>>> def test_gen():
...     x = 2
...
...     print('first yield')
...     yield x
...
...     print('second yield')
...     x *= 2
...     yield x
...
...     print('thid yield')
...     x *= 2
...     yield x * 2
...
>>> g = test_gen()
>>> next(g)
first yield
2
>>> next(g)
second yield
4
>>> next(g)
thid yield
8
```

循环调用 next：
```python
>>> fib = fibonacci(5)
>>> try:
...     while True:
...             print(next(fib))
... except:
...     pass
...
1
2
3
5
8
```

生成器对象也是可迭代对象，使用 for 循环时会隐式的调用 next() 方法：
```python
>>> fib = fibonacci(5)
>>> for s in fib:
...     s
...
1
2
3
5
8
```

将可迭代对象转换为生成器：
```python
>>> s = [1,2,3,4,5,6]
>>> def gr():
...     for a in s:
...             yield a
...
>>> g = gr()
>>> next(g)
1
>>> next(g)
2
>>> next(g)
3
>>> for i in g:
...     i
...
4
5
6
```

### Generator Expression

生成器表达式

类似于 lambda 表达式创建匿名函数，也可以使用生成器表达式来创建匿名的生成器函数，
其语法和列表推导语法一样，只是把方括号改为了小括号：
```python
gen_expr = (var**(1/2) for var in seq)
```

例如：
```python
>>> g = (s*2 for s in [1,2,3,4])
>>> next(g)
2
>>> next(g)
4
>>> for s in g:
...     s
...
6
8
```

生成器表达式可以组合：
```python
>>> lst = [2, 3, 4, 5]
>>> itr = ((x*2, x**2) for x in lst)
>>> next(itr)
(4, 4)
>>> next(itr)
(6, 9)
>>> next(itr)
(8, 16)
>>> next(itr)
(10, 25)
```

和列表推导还有个不同点，列表一次性返回所有值，而生成器一次返回一个值：
```python
>>> ls = [2,4,8,16]
>>> square_root = [s**1/2 for s in ls]
>>> square_root2 = (s**1/2 for s in ls)
>>> print(square_root)
[1.0, 2.0, 4.0, 8.0]

>>> print(square_root2)
<generator object <genexpr> at 0x000001D8E7CF3318>
>>> next(square_root2)
1.0
>>> next(square_root2)
2.0
```

## Generator Vs. Function

生成器和常规函数之间的区别：
* 生成器使用 yield 语句将值返回给调用者，而函数则使用 return。
* 生成器函数可以有一个或多个 yield 调用。
* yield 调用会暂停执行并返回一个迭代器，而 return 语句是最后一个要执行的语句。
* next() 方法的调用触发了生成器函数的执行。
* 局部变量及其状态在对 next() 方法的连续调用之间保持不变。
* 如果生成器已没有更多项，则对 next() 的任何额外调用都会引发 StopIteration 异常。

## When To Use A Generator?

在许多用例中，生成器都是有用的，其中一些:
* 生成器可以帮助处理大量数据。它们可以让我们在需要的时候进行计算，也称为延迟求值
  。流处理就使用了这种方法。
* 我们还可以将生成器一个一个地堆叠起来，就像我们使用 Unix 管道一样使用它们。
* 生成器还可以让我们建立并发性。
* 我们可以使用生成器来读取大量的大文件。通过将整个过程分割成更小的实体，它将有助
  于保持代码更干净、更精简。
* 生成器对于 web 抓取非常有用，有助于提高爬行效率。它们可以让我们获取单个页面，
  执行一些操作，然后进入下一个页面。这种方法比一次检索所有页面然后使用另一个循环
  来处理它们要有效和直观得多。

## Why Use Generators?

### 1. Programmer-Friendly Feature

生成器是实现迭代器的完美选择。

生成器的逻辑比迭代类更简单，可以轻松地将它们合并到程序中。

使用迭代类实现：
```python
class AP:
    def __init__(self, a1, d, size):
        self.ele = a1
        self.diff = d
        self.len = size
        self.count = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.count >= self.len:
            raise StopIteration
        elif self.count == 0:
            self.count += 1
            return self.ele
        else:
            self.count += 1
            self.ele += self.diff
            return self.ele

for ele in AP(1, 2, 10):
    print(ele)
```

使用生成器实现：
```python
def AP(a1, d, size):
    count = 1
    while count <= size:
        yield a1
        a1 += d
        count += 1

for ele in AP(1, 2, 10):
    print(ele)
```

### 2. Memory Agnostic

如果我们使用一个常规函数返回一个列表，那么在将它发送给调用者之前需要在内存中形成
完整的序列。这样的操作将导致高内存使用，并变得极其低效。

相反，使用生成器将消耗更少的内存，并且您的程序将变得更高效，因为它一次只处理一项。

### 3. Capable Of Handling Big Data

如果您必须处理诸如 Big Data 之类的巨大数据，那么生成器是非常有用的。它们可以作为
一个无限的数据流。

我们不能在内存中包含这么大的数据。但是每次只返回一个值的生成器确实代表了无限的数
据流。

下面的代码理论上可以产生所有质数：
```python
def find_prime():
    num = 1
    while True:
        if num > 1:
            for i in range(2, num):
                if (num % i) == 0:
                    break
            else:
                yield num
        num += 1

for ele in find_prime():
    print(ele)
```

### 4. Generator Pipeline

在生成器的帮助下，我们可以创建不同操作的管道。这是一种更干净的方法，可以在各个组
件之间细分职责，然后将它们集成在一起，以实现预期的结果。

在下面的示例中，我们链接了两个函数，第一个函数查找1到100之间的质数，而第二个函数
从它们中选择奇数：
```python
def find_prime():
    num = 1
    while num < 100:
        if num > 1:
            for i in range(2, num):
                if (num % i) == 0:
                    break
            else:
                yield num
        num += 1

def find_odd_prime(seq):
    for num in seq:
        if (num % 2) != 0:
            yield num

a_pipeline = find_odd_prime(find_prime())

for a_ele in a_pipeline:
    print(a_ele)
```

