---
title: Python - Function
categories:
- Python
tags:
- Python
- Python Function
date: 2019-05-13 13:16:01
---

Python - Function

<!--more-->

## 创建函数

单行函数
```python
def single_line(): statement
```

带文档字符串的函数
```python
def fn(arg1, arg2,...):
    """docstring"""
    statement1
    statement2
```

嵌套函数
```python
def fn(arg1, arg2,...):
    """docstring"""
    statement1
    statement2
    def fn_new(arg1, arg2,...):
        statement1
        statement2
        ...
    ...
```

def 关键字是个语句，用于创建函数对象，并把函数对象赋值给后面的函数名，可以进一步
把相同的函数对象赋值给其他变量：
```python
>>> def test6():
...     print('test6 created')
...
>>> test7 = test6
>>> test7()
test6 created
```

使用 def 语句创建函数，因为 def 是个语句，所以语句可以出现的地方都可以创建函数，
例如函数中或 if 语句块里：
```python
>>> if 1 == 1:
...     def test3():print('test3 created')
... else:
...     def test4():print('test4 created')
...
>>> test3()
test3 created

>>> test4()
NameError: name 'test4' is not defined
```

??? if 语句块里的函数是全局作用域
??? 函数里嵌套的函数是全局作用域
```python
>>> def outer(n):
...     if n % 2 == 0:
...             def odd_or_even():
...                     print('even')
...     else:
...             def odd_or_even():
...                     print('odd')
...     odd_or_even()
...
>>> outer(1)
odd
>>> outer(2)
even
>>> odd_or_even()
NameError: name 'odd_or_even' is not defined
```

## 调用函数

### 多态

在 Python 中，函数多态性是可能的，因为我们在创建函数时没有指定参数类型：
```python
>>> def product(x,y):
...     return x * y
...
>>> product(2,3)
6
>>> product('2',3)
'222'

>>> product([1,2,3], 3)
[1, 2, 3, 1, 2, 3, 1, 2, 3]
```
* 函数的行为可能会根据传递给它的参数而变化。
* 同一个函数可以接受不同对象类型的参数。
* 如果对象找到匹配的接口，函数可以处理它们。

关于 Python 多态：
* Python 是一种动态类型语言，这意味着类型与值相关，而不是与变量相关。因此，多态
  性可以不受限制地运行。
* 这是 Python 与其他静态类型语言(如 c++ 或 Java)之间的主要区别之一。
* 在 Python 中，编写代码时不必提及特定的数据类型。
* 但是，如果您这样做了，那么代码将限制为编码时预期的类型。
* 这样的代码不允许将来可能需要的其他兼容类型。
* Python 不支持任何形式的函数重载。

## 函数参数

我们经常交替使用 parameters 和 arguments 这两个术语。然而，它们之间有一个细微的
区别：parameters 是函数定义中使用的变量（形参），而 arguments 是调用函数时传递给
函数参数的值（实参）。

Python 支持将参数传递给函数的不同变体。

关于参数的几点：
* 参数一旦传递给函数，就被分配局部变量名。
* 在函数中更改参数的值不会影响调用者。
* 如果参数包含一个可变对象，那么在函数中更改它会影响调用者。
* 我们将不可变参数的传递称为值传递，因为 Python 不允许它们改变。
* 可变参数的传递恰好是通过 Python 中的指针传递，因为它们可能会受到函数内部变化的影响。

### 不可变参数和可变参数

```python
>>> def test1(a, b):
...     a = 'aaa'
...     b = 'bbb'           # b 接受的本来是可变对象，但在这里被赋予了新的对象
...
>>> def test2(a, b):
...     a = 'aaa'
...     b[0] = 'bbb'        # b 是可变对象，可直接更改其值
...
>>> p1 = 'p1p1p1'
>>> p2 = ['p2','p22','p222']

>>> test1(p1, p2)
>>> p1
'p1p1p1'
>>> p2
['p2', 'p22', 'p222']

>>> test2(p1, p2)
>>> p1
'p1p1p1'
>>> p2
['bbb', 'p22', 'p222']
```

如果需要为函数传递可变参数，如一个列表，但又不希望函数修改可变参数的值，可以通过
切片传递可变参数的副本：
```python
>>> def test2(a, b):
...     a = 'aaa'
...     b[0] = 'bbb'
...
>>> p1 = 'p1'
>>> p2 = ['p2','p22']
>>> test2(p1, p2[:])        # 这里传递的是可变对象的副本
>>> p1
'p1'
>>> p2
['p2', 'p22']
```

### 关键字参数

调用函数时，根据位置被逐一赋值的参数称为位置参数(positional argument)，此外还有
关键字参数(keyword argument)

在调用函数时，用这种形式(`param1=value1, param2=value2`)为参数赋值并传递给函数(
如`fn(param1=value1, param2=value2)`) 时，参数将变成一个关键字参数。

如果将关键字参数传递给函数，那么 Python 将通过调用时使用的参数名来确定其所需的参
数，这时调用参数的顺序也可改变：
```python
>>> def test(aa, bb):
...     print(aa, bb)
...
>>> test(aa = 'value1', bb = 'value2')
value1 value2

>>> test(bb = 'value2', aa = 'value1')
value1 value2
```

关键字参数必须位于位置参数后面：
```python
>>> def test(aa, bb):
...     print(aa, bb)
...
>>> test(aa = 'hello','world')
SyntaxError: positional argument follows keyword argument

>>> test('world',aa = 'hello')
TypeError: test() got multiple values for argument 'aa'

>>> test('hello',bb = 'world')
hello world
```

在使用关键字参数时，应该确保赋值中的名称与函数定义中的名称匹配。否则，Python 将
抛出类型错误：
```python
>>> test(aaa = 'value1', bb = 'value2')
TypeError: test() got an unexpected keyword argument 'aaa'
```

### 参数默认值

函数定义时可以指定参数的默认值：
```python
>>> def test(aa='hello'):
...     print(aa)
...
>>> test()
hello
>>> test('world')
world
```

多个参数时，有默认值的参数必须位于没有默认值的参数后面，否则：
```python
>>> def test(aa='hello',bb):
...     print(aa,bb)
...
  File "<stdin>", line 1
SyntaxError: non-default argument follows default argument

>>> def test(bb, aa='hello'):
...     print(aa,bb)
...
>>> test('ss')
hello ss
```

### 长度可变参数

要定义像 print 函数一样，可以接受可变化参数数量的函数，可以通过长度可变参数，即
在参数前面加 `*` 号，语法：
```python
def fn([std_args,] *var_args_tuple ):
   """docstring"""
   function_body
   return_statement
```

调用时可忽略长度可变参数：
```python
>>> def test(aa, *bb):
...     print(aa,':')
...     for s in bb:
...             print(s)
...
>>> test('books','java','python','go')
books :
java
python
go

>>> test('books')
books :
```

若长度可变参数位于其他普通参数前面，则后面的参数需通过关键字参数来传递：
```python
>>> def test(*bb, aa):
...     print(aa, ':')
...     for s in bb:
...             print(s)
...
>>> test('a','b','c','d')
TypeError: test() missing 1 required keyword-only argument: 'aa'

>>> test('a','b','c',aa='d')
d :
a
b
c
```

## 函数中的局部变量

In Python, the variables assignment can occur at three different places.

Inside a def – it is local to that function
In an enclosing def – it is nonlocal to the nested functions ???
Outside all def(s) – it is global to the entire file

```python
>>> def outer():
...     a = 10
...     def inner():
...             b = 20
...             print(a, b)
...     inner()
...     print(a,b)  # 这里无法读取 b
...
>>> outer()
10 20
Traceback (most recent call last):
NameError: name 'b' is not defined
```

## 函数中的全局变量

函数中可访问全局变量，但若在函数中对和全局变量同名的变量**赋值**，则默认是创建一
个新的局部变量：
```python
>>> x = 10
>>> id(x)
140726150751344

>>> def test():
...     print(x)
...     print(id(x))
...
>>> test()
10
140726150751344     ## 和全局变量 x 地址相同

>>> def test2():
...     x = 20
...     print(x)
...     print(id(x))
...
>>> test2()
20
140726150751664     ## 和全局变量 x 地址 不 相同
```

若确认是要在函数中操作全局变量，而不是创建新的同名局部变量，可以使用 global 关键
字，此关键字也是个语句，在一条 global 语句中可以指定一个或多个用逗号分隔的变量名
，
```python
>>> x = 10
>>> id(x)
140726150751984

>>> def test3():
...     global x
...     x = 30
...     print(id(x))
...
>>> test3()
140726150751984
>>> x
30
```

### global 和 import

```python

```

## 函数中的名称解析

LEGB 规则代表了函数中变量的优先解析顺序：
* L locals      函数内的名字空间，包括局部变量和形参
* E enclosing   外部嵌套函数的名字空间（闭包中常见）
* G globals     全局变量，函数定义所在模块的名字空间
* B builtins    内置模块的名字空间

逐个取消下面 var 的注释：
```python
#var = 5
def fn1() :
   #var = [3, 5, 7, 9]
   def fn2() :
      #var = (21, 31, 41)
      print(var)
   fn2()
fn1()
print(var)
```
则输出分别为：
```python
5
5

[3, 5, 7, 9]
5

(21, 31, 41)
5
```

## 函数中的作用域查找

Python 函数可以访问所有位于闭合 def 语句中的名称：
```python
X = 101         # global scope name - unused
def fn1():
   X = 102      # Enclosing def local
   def fn2():
      print(X)  # Reference made in nested def
   fn2()        # Prints 102: enclosing def local

fn1()
```

即使所包含的函数已经返回，作用域查找仍然有效。
```python
>>> def outer():
...     x = 10
...     def inner():
...             print(x)
...     return inner
...
>>> f = outer()
>>> f()
10
```

要为 `d[0]` 赋值时，
```python
>>> def test():
...     d = [1]
...     def test2():
...             d[0] = 2
...     test2()
...     print(d)
...
>>> test()
[2]

>>> def test():
...     d = 1
...     def test2():
...             d = 2
...     test2()
...     print(d)
...
>>> test()
1
```

python3 支持 `nonlocal`，python2 使用可变变量来实现类似功能：
```python

```

`global` 和 `nonlocal` 
```python

```

赋值和读取：
```python

```


## 函数的返回值

通常，函数返回一个值。但如果需要，Python允许使用集合类型(如使用元组或列表)返回多
个值。
```python
>>> def test():
...     return 'hello','world'
...
>>> a,b = test()
>>> a
'hello'
>>> b
'world'
```

通过返回元组并将结果分配回调用者中的原始参数名，该特性的工作原理类似于引用调用。
```python
>>> def returnDemo(val1, val2) :
>>>    val1 = 'Windows'
>>>    val2 = 'OS X'
>>>    return val1, val2 # return multiple values in a tuple

>>> var1 = 4
>>> var2 = [2, 4, 6, 8]

>>> print("before return =>", var1, var2)
before return => 4 [2, 4, 6, 8]

>>> var1, var2 = returnDemo(var1, var2)

>>> print("after return  =>", var1, var2)
after return => Windows OS X
```

## 作为对象的函数

Python 把所有的东西都当作对象，函数也一样。

可以把函数对象赋值给其他变量：
```python
>>> def test(a, b): print(a, b)
>>> fn = test
>>> fn('hello','world')
hello world
```

甚至可以把函数对象作为参数传递给其他函数：
```python
>>> def fn(a, b):
...     a(b)
...
>>> def test(s):
...     print(s)
...
>>> fn(test,'hello, world')
hello, world
```

还可以把函数对象嵌入到数据结构中：
```python
>>> def fn1(a): print('fn1',a)
...
>>> def fn2(a): print('fn2',a)
...
>>> ls = [(fn1, 'hello'),(fn2, 'world')]
>>> for fn,arg in ls:
...     fn(arg)
...
fn1 hello
fn2 world
```

可以从函数中返回另一个函数对象：
```python
>>> def funcs(n):
...     def fn1(): print('fn1 called')
...     def fn2(): print('fn2 called')
...     def fn3(): print('fn3 called')
...     if n == 1: return fn1
...     elif n == 2: return fn2
...     else: return fn3
...
>>> funcs(1)
<function funcs.<locals>.fn1 at 0x0000028AD08E96A8>

>>> f = funcs(1)
>>> f()
fn1 called

>>> funcs(1)()
fn1 called
>>> funcs(2)()
fn2 called
```

## 函数属性

Python 的函数可以有属性，函数的属性可以是系统定义的，也可以是用户定义的。

函数属性不是函数的内部变量，如用户定义的函数属性是使用 `func_name.attr = val` 定
义的。

可以使用 `dir()` 函数同时查看系统和用户定义的属性：
```python
>>> def test():
...     a = 10
...     print(a)
...
>>> dir(test)
['__annotations__', '__call__', '__class__', '__closure__', '__code__',
'__defaults__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__',
'__format__', '__ge__', '__get__', '__getattribute__', '__globals__', '__gt__',
'__hash__', '__init__', ' __init_subclass__', '__kwdefaults__', '__le__',
'__lt__', '__module__', '__name__', '__ne__', '__new__', '__qualname__',
'__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__',
'__str__', '__subclasshook__']

>>> test.attr1 = 'b'
>>> test.attr2 = 'c'
>>> dir(test)
['__annotations__', '__call__', '__class__', '__closure__', '__code__',
'__defaults__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__',
'__format__', '__ge__', '__get__', '__getattribute__', '__globals__', '__gt__',
'__hash__', '__init__', ' __init_subclass__', '__kwdefaults__', '__le__',
'__lt__', '__module__', '__name__', '__ne__', '__new__', '__qualname__',
'__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__',
'__str__', '__subclasshook__', 'attr1', 'attr2']
>>>
```

可以使用函数属性来代替任何全局变量或非局部变量存档状态信息，与非局部变量不同，函
数属性可以在函数本身所在的任何位置访问，甚至可以从代码外部访问：
```python

```

