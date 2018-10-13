---
title: MySQL - Manual - 11.9 - Data Types - 为列选择正确的数据类型
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 为列选择正确的数据类型

<!--more-->

为了获得最佳存储，您应该在所有情况下尝试使用最精确的类型。例如，如果一个整数列值
范围从 1 到 99999，那么 `MEDIUMINT UNSIGNED` 是最好的类型。因为在可以表示所有必
需值的类型中，这种类型使用最少的存储空间。所有 `DECIMAL` 列的基本计算（`+，-，*
，/`）都精确到 65 位小数（基数 10）(with precision of 65 decimal (base 10)
digits)。参考 Section 11.1.1, “Numeric Type Overview”.

如果精确度不是很重要，或者速度是最高优先级，那么 `DOUBLE` 类型可能已经足够好了。
为了实现高精确度，您总是可以转换为存储在 `BIGINT` 中的定点类型。这使您能够使用
64 位整数进行所有的计算，然后根据需要将结果转换回浮点值。
