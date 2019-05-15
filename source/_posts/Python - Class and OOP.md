---
title: Python - Class and OOP
categories:
- Python
tags:
- Python
- Class
- OOP
date: 2019-05-15 14:42:09
---

Python - Class and OOP

<!--more-->

## Python and OOP

Python 支持面向对象编程(OOP)。

OOP是一种开发模型，它让程序员专注于生成可重用代码。它不同于遵循顺序方法的过程模型。

当您要处理一个大型且复杂的项目时，OOP非常有用。将有多个程序员创建可重用的代码，
共享和集成他们的源代码。可重用性带来更好的可读性，并在长期内减少维护。

## Class

类是将变量和函数排列成一个逻辑实体。

Python 有一个名为 class 的保留关键字，您可以使用它来定义一个新类。

类作为创建对象的模板。对象是在运行时创建的类的工作实例。每个对象都可以使用类变量
和函数作为其成员。

## The “class” keyword

使用 class 关键字定义类，注意是小写：
```python
class Class_Name:
    Class_Body
```

## The “self” keyword

`self` 关键字表示类的实例，可以从类的方法中访问类成员(如类的属性)。

另外，请注意，它隐式地是每个 Python 类中 `__init__` 方法的第一个参数。

## The “__init__” method

`__init__` 是与每个 Python 类关联的唯一方法。

Python 为每个通过类创建的对象自动调用此方法。它的目的是用用户提供的值初始化类属
性。

在面向对象编程中通常称为构造函数。

```python
>>> class Dogs:
...     def __init__(self):
...             print('constructor get called')
...
>>> d = Dogs()
constructor get called
```

## The instance attributes

特定于对象的属性被定义为 `__init__` 方法的参数，每个对象可以有自己不同的值：
```python
>>> class Dogs:
...     def __init__(self,name,color):
...             self.name = name
...             self.color = color
...
>>> d = Dogs('wangwang', 'black')
>>> e = Dogs('aoaoao', 'white')
```

## The class attributes

与对象级可见的实例属性不同，类属性对于所有对象都是相同的，可以直接使用类名访问：
```python
>>> class Dogs:
...     legs = 4
...     number = 0
...
...     def __init__(this, name, color):
...             this.name = name
...             this.color = color
...             Dogs.number += 1
...
>>> d = Dogs('aoaoao', 'white')
>>> e = Dogs('wangwang', 'black')
>>> Dogs.number
2
```

