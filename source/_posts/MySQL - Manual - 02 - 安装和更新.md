---
title: MySQL - 安装和更新
categories: 
 - MySQL
tags: 
 - MySQL
---

MySQL 安装和更新

<!--more-->

## 版本

### DMR

Development Milestone Release 开发里程碑版

### RC

Release Candidate 候选版本

### GA

General Availability Releases 通用版本，也被称为 stable release 稳定版或
production release 产品发布版

GA 版本是推荐生产系统使用的版本，每 18 到 24 个月发布一次，GA 版本流程：

    1. GA 版本基于 DMR
    2. 选定的 DMR 经过额外的测试和 Bug 修复，产生了 RC 版本
    3. RC 版本经过用户和客户的使用评价，经过多轮 RC 周期
    4. 达到 GA 版本质量标准后，对外发布 GA 版本

即:里程碑版本 --》 RC 版本 --》 GA 版本

## 版本号说明

MySQL 5.7 的命名方案组成：3 组数字和 1 个可选的后缀，如：mysql-5.7.1-m1，分别是

    1. 主版本号
    2. 次版本号 ，主版本号和次版本号共同组成了版本的序列号，序列号描述了稳定的特
       性集合
    3. 修订版本号，每次修复 bug 后重新发布版本时都会增加
    4. 版本的稳定性级别，可选项有：
        4.1 mN ，如 m1,m2,m3...表示 milestone number，里程碑版本，每个里程碑都会
        引入小部分新特性，从这个里程碑到下个里程碑接口会变化或被移除
        4.2 rc ，候选版本，已通过了内部测试较稳定，新特性仍可能引入，但焦点转移
        到修复 bug
        4.3 没有这个可选项表示是最稳定的 GA 版


## 选择要安装的版本和格式

先考虑安装版本： GA 版还是开发版

                 开发版虽然拥有最新的特性，但不建议生产环境使用

再考虑发行格式：二进制包还是源码包

                二进制包采用的是本机格式，如 linux 平台的 RPM 包，OS X 平台的
                DMG 包，Windows 平台则可以通过 MySQL Installer 程序安装

                某些情况下使用源码包更好：
                1. 想把 MySQL 安装到指定目录
                2. 配置 mysqld 包含某些儿特性，而标准二进制包不包含这些特性配置
                   ，常用的额外特性：
                   2.1. `-DWITH_LIBWRAP=1` 支持 TCP 封装器
                   2.2. ` -DWITH_ZLIB={system|bundled}` 应用于依赖于压缩的特性
                   2.3. `-DWITH_DEBUG=1` 支持 debug
                3. 配置 mysqld 不包含某些儿特性，如标准的二进制包包含了所有的字
                   符集，通过源代码包，可以配置只包含自己使用的字符集从而获得最
                   小安装的 MySQL 服务器
                4. 想要阅读 MySQL 的 C 和 C++ 代码
                5. 源码包比二进制包包含更多测试和样例

## 获取 MySQL

基于 RPM 的 Linux 平台使用 MySQL YUM 源
基于 Debian 的 Linux 平台使用 MySQL APT 源
SUSE Linux Enterprise Server (SLES) 平台使用 MySQL SLES 源

## 完整性校验

有 3 种完整性校验方法：
1. MD5 校验和
2. GPG 加密签名校验
3. RPM 内置完整性校验

### MD5

各个平台都有自己的校验和实现，一般名称都是 md5 或 md5sum

校验和是一个16进制的字符串

使用平台对应的软件计算校验和然后和下载页列出的校验和比对

注意校验的是未被解压的 .zip, .tar, .msi 等归档文件，而不是解压归档文件后的内容

#### Linux 平台

如果已安装 OpenSSL，则可以
```
shell> openssl md5 package_name 
```

否则可以使用  GNU Text Utilities ， MD5 校验是它功能的一部分
http://www.gnu.org/software/textutils/
```
shell> md5sum mysql-standard-5.7.21-linux-i686.tar.gz
aaab65abbec64d5e907dcd41b8699945 mysql-standard-5.7.21-linux-i686.tar.gz
```

#### Windows 平台

命令行工具
http://www.fourmilab.ch/md5/
```
shell> md5.exe mysql-installer-community-5.7.21.msi
aaab65abbec64d5e907dcd41b8699945 mysql-installer-community-5.7.21.msi
```

图形化工具 WinMD5Sum
http://www.nullriver.com/index/products/winmd5sum

### 数字签名

数字签名比 MD5 更可靠，但也更复杂一些儿

#### 获取并导入公钥

MySQL 使用 GPG 签名，验证签名前需要先获得公钥

可以直接使用命令从公钥服务器上下载，MySQL 的公钥 ID 为：5072E1F5
```bash
shell> gpg --recv-keys 5072E1F5
```

或者去此公钥服务器 ：http://pgp.mit.edu/ 根据公钥名：mysql-build@oss.oracle.com
查找到公钥后保存为文件，例如：mysql_pubkey.asc，然后将此公钥导入 GPG 钥匙圈
```bash
shell> gpg --import mysql_pubkey.asc
```

还可以把公钥文件导入 RPM 的配置，用于 RPM 的内部验证机制
```bash
shell> rpm --import mysql_pubkey.asc
```
???public build key 公共构建密钥

#### 下载软件包和对应签名文件

签名文件名为软件包名后面加 `.asc`，例如软件包名：
`mysql-standard-5.7.21-linux-i686.tar.gz`

则签名文件名为：
`mysql-standard-5.7.21-linux-i686.tar.gz.asc`

#### Linux 下验证签名

将软件包和其对应的签名文件下载保存到同一目录，运行验证命令
```bash
shell> gpg --verify mysql-standard-5.7.21-linux-i686.tar.gz.asc
gpg: Signature made Tue 01 Feb 2011 02:38:30 AM CST using DSA key ID 5072E1F5
gpg: Good signature from "MySQL Release Engineering <mysql-build@oss.oracle.com>"
```

如上所示提示 `Good signature` 则说明签名验证通过，但如果提示 `Warning`
```bash
shell> gpg --verify mysql-standard-5.7.21-linux-i686.tar.gz.asc
gpg: Signature made Wed 23 Jan 2013 02:25:45 AM PST using DSA key ID 5072E1F5
gpg: checking the trustdb
gpg: no ultimately trusted keys found
gpg: Good signature from "MySQL Release Engineering <mysql-build@oss.oracle.com>"
gpg: WARNING: This key is not certified with a trusted signature!
gpg: There is no indication that the signature belongs to the owner.
Primary key fingerprint: A4A9 4068 76FC BD3C 4567 70C8 8C71 8D3B 5072 E1F5
```

其实也是正常现象，因为这取决于系统的配置：

1. `gpg: no ultimately trusted keys found
    gpg: Good signature from "MySQL Release Engineering <mysql-build@oss.oracle.com>"`

    表示指定的公钥不是完全可信任的，但用于验证签名还是可以的。

2. `gpg: WARNING: This key is not certified with a trusted signature!
    gpg: There is no indication that the signature belongs to the owner.`

    密钥没有经过可信签名的认证，没有证据表明签名属于所有者，

    This refers to your level of trust in your belief that you possess our real public key. 
    This is a personal decision.
    Ideally, a MySQL developer would hand you the key in person,
    but more commonly, you downloaded it. Was the download tampered with? Probably not, but this decision is up to you.
    Setting up a web of trust is one method for trusting them.

#### Windows 下验证签名

Windows 下使用 Gpg4win 搭配 Kleopatra 完成

### RPM

RPM 软件包没有独立的签名，RPM 内置 GPG 校验和 MD5 校验和

```bash
shell> rpm --checksig MySQL-server-5.7.21-0.linux_glibc2.5.i386.rpm
MySQL-server-5.7.21-0.linux_glibc2.5.i386.rpm: md5 gpg OK
```

在 RPM 4.1 中，若你已把公钥导入了自己的 GPG 密匙环，仍提示 `(GPG) NOT OK
(MISSING KEYS: GPG#5072e1f5)`，则因为 RPM 4.1 不再使用用户自己的 GPG 密钥环，而
是由 RPM 自身维护一个密钥环，因为 RPM 是一个系统级的程序，而用户的密钥环是特定于
某个用户的，所以发生此问题时，需要把公钥导入到 RPM 自身的密钥环。

```bash
shell> gpg --export -a 5072e1f5 > 5072e1f5.asc
shell> rpm --import 5072e1f5.asc
```

RPM 也支持直接从 URL 下载公钥
```
shell> rpm --import http://dev.mysql.com/doc/refman/5.7/en/checking-gpg-signature.html
```

## 安装布局

不同类型的安装包布局是不一样的，即源码包、二进制包、本地包的安装布局不同。

• Section 2.3.1, “MySQL Installation Layout on Microsoft Windows”
• Section 2.9.1, “MySQL Layout for Source Installation”
• Table 2.3, “MySQL Installation Layout for Generic Unix/Linux Binary Package”
• Table 2.10, “MySQL Installation Layout for Linux RPM Packages from the MySQL Developer Zone”
• Table 2.6, “MySQL Installation Layout on OS X”

## 使用通用二进制软件包安装



## 创建名为 mysql 的用户和用户组

```
shell> groupadd mysql
shell> useradd -r -g mysql -s /bin/false mysql
```

## 创建名为 mysql 的用户和用户组

```
shell> groupadd mysql
shell> useradd -r -g mysql -s /bin/false mysql
```

