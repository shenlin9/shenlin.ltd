---
title: Python - Inheritance and OOP
categories:
- Python
tags:
- Python
- Inheritance
date: 2019-05-15 16:01:14
---

Python - Inheritance and OOP

<!--more-->

## Inheritance

继承是面向对象编程的核心特性，它通过添加新特性来扩展现有类的功能。

可以将其与现实情况进行比较，例如孩子除了自己的财产外，还继承了父母的财产。甚至从
他的父母那里得到姓氏。

通过使用继承特性，我们可以有一个带有旧属性的新蓝图，但不需要对原始蓝图做任何更改
。我们将新类称为派生类或子类，而旧类则成为基类或父类。

继承的语法如下：
```python
class ParentClass:
  Parent class attributes
  Parent class methods

class ChildClass(ParentClass):
  Child class attributes
  Child class methods
```

## The Super() Method

`super()` 方法允许我们访问继承的方法，这些方法都是通过继承级联到类对象的。

如果在子类中需要调用父类的方法：
```python
super().parent_func()
```

同样的，可以在子类的构造函数中调用父类的构造函数：
```python
super().__init__()
```

## Exmaple

```python
class Taxi:

    def __init__(self, model, capacity, variant):
        self.__model = model      # __model is private to Taxi class
        self.__capacity = capacity
        self.__variant = variant

    def getModel(self):          # getmodel() is accessible outside the class
        return self.__model

    def getCapacity(self):         # getCapacity() function is accessible to class Vehicle
        return self.__capacity

    def setCapacity(self, capacity):  # setCapacity() is accessible outside the class
        self.__capacity = capacity

    def getVariant(self):         # getVariant() function is accessible to class Vehicle
        return self.__variant

    def setVariant(self, variant):  # setVariant() is accessible outside the class
        self.__variant = variant

class Vehicle(Taxi):

    def __init__(self, model, capacity, variant, color):
        # call parent constructor to set model and color  
        super().__init__(model, capacity, variant)
        self.__color = color

    def vehicleInfo(self):
        return self.getModel() + " " + self.getVariant() + " in " + self.__color + " with " + self.getCapacity() + " seats"

# In method getInfo we can call getmodel(), getCapacity() as they are 
# accessible in the child class through inheritance

v1 = Vehicle("i20 Active", "4", "SX", "Bronze")
print(v1.vehicleInfo())
print(v1.getModel()) # Vehicle has no method getModel() but it is accessible via Vehicle class

v2 = Vehicle("Fortuner", "7", "MT2755", "White")
print(v2.vehicleInfo())
print(v2.getModel()) # Vehicle has no method getModel() but it is accessible via Vehicle class
```
