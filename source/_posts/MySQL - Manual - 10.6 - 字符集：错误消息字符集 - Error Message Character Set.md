---
title: MySQL - Manual - 10.6 - 字符集：错误消息字符集 - Error Message Character Set
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 字符集：错误消息字符集

<!--more-->

本节描述 MySQL 服务器如何使用字符集来构造错误消息并将其返还给客户端。有关错误消
息的语言（而不是字符集）的信息，请参见 Section 10.11, “Setting the Error
Message Language”。有关配置错误日志的一般信息，请参阅 Section 5.4.2, “The
Error Log”。

服务器使用 UTF-8 构造错误消息，并使用由系统变量 `character_set_results` 指定的字
符集将它们返回到客户端。客户端可以设置 `character_set_results` 来控制接收错误消
息的字符集。该变量可以直接设置，也可以通过 `SET NAMES` 等方式间接设置。有关
`character_set_results` 的更多信息，请参见 Section 10.4, “Connection Character
Sets and Collations”。

服务器构造错误消息如下所述：

* 消息模板使用UTF-8。
* 消息模板中的参数被替换为适用于特定错误事件的值：
    * 诸如表或列名之类的标识符在内部使用 UTF-8，因此它们是按照原样复制的。
    * 字符（非二进制）字符串值从它们的字符集转换成 UTF-8。
    * 字节范围从 `0x20` 到 `0x7E` 的二进制字符串值被按照原样复制，范围之外的字节
      使用 `\x` 十六进制编码。例如,如果试图将 `0x41CF9F` 插入 `VARBINARY` 唯一列
      时发生了重复键错误，产生的错误消息使用 UTF-8 与一些十六进制编码的字节:
        ```
        Duplicate entry 'A\xC3\x9F' for key 1
        ```

消息构建完后要返回给客户端，服务器把它从 UTF-8 转换为系统变量
`character_set_results` 指定的字符集。如果 `character_set_results` 值为 `NULL`
或 `binary`，则不进行转换。值为 UTF-8 也不进行转换，因为匹配原始的错误消息字符集
。

对于不能在 `character_set_results` 中表示的字符，在转换过程中可能会使用一些编码
。编码使用 Unicode 代码点（code point）值：

* 基本多语平面 （Basic Multilingual Plane，BMP）范围 (`0x0000` to `0xFFFF`) 内的
  字符使用 `\nnnn` 符号书写.

* BMP 范围外(`0x10000` to `0x10FFFF`)的字符使用 `\+nnnnnn` 符号书写.

> 关于代码点，code point，参考：https://en.wikipedia.org/wiki/Code_point，和 point
> code 是两个不同的概念
