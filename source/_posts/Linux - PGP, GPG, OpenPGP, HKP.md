---
title: Linux - PGP, GPG, OpenPGP, HKP
categories:
  - Linux
tags:
  - Linux
  - PGP
  - GPG
  - OpenPGP
  - HKP
---

GPG 大多用于加密信息在不安全网络环境下的传递（如 internet）。

如果仅仅是想在本地加密数据，推荐使用 ![eCryptfs(The enterprise cryptographic
filesystem for Linux)](http://ecryptfs.org/) 企业级加密文件系统（ubuntu安装时有
个选项——加密用户文件夹，使用的就是该系统）。

<!--more-->

## RSA

RSA公钥加密。RSA公钥加密算法基于一个简单的事实，就是将两个大质数相乘很容易，但是
想要在不知情的情况下根据结果找出是由哪两个数相乘得到的该结果却不容易，至少在当今
一段时间和过去想要暴力破解使用了长秘钥的RSA加密信息是相当困难的。

## PGP 和 GPG

1991年，程序员Phil Zimmermann为了避开政府的监视，开发了加密软件PGP (Pretty Good
Privacy)。

但 PGP 是商业软件，所以，自由软件基金会开发了PGP的替代品开源软件 GnuPG，即GNU
Privacy Guard，GNU 隐私卫士，也常缩写为GPG。

大多数的linux发行版都默认包含了 gpg

Windows 版 GPG：https://www.gpg4win.org/

## OpenPGP

OpenPGP 最广泛使用的电子邮件加密标准，

由 ![Philip Zimmermann](https://philzimmermann.com/EN/background/index.html) 开发 PGP 软件时首次实现。

https://www.openpgp.org/

## GnuPG

GnuPG 是一个开放源码的与 OpenPGP 标准兼容的加密系统. 完全免费和自由, 避免了使用 RSA 和其他获得专利的算法.



GnuPG是GNU将OpenPGP（即PGP）中的专利算法去除后实现的开源软件。GnuPG整合了RSA公钥加密和数字签名技术，可以运行在Windows或Linux系统中，可以用来加密文件或者邮件。

## keyserver 

公钥服务器

公钥需要先导入本地才能使用，没有对应的公钥则需要到公钥服务器下载和导入

PGP公钥服务器列表：

    http://keyserver.ubuntu.com
    http://keys.gnupg.net
    hkp://subkeys.pgp.net
    hkp://pgp.mit.edu
    hkp://pool.sks-keyservers.net
    hkp://zimmermann.mayfirst.org

查看所有公钥服务器上的公钥数目，可访问网站：https://sks-keyservers.net/status/

## HKP

HKP : OpenPGP HTTP Keyserver Protocol

hkp 是基于 http 协议实现的， hkp 协议默认端口 11371, 

因此 hkp 服务器也支持以 http 指定端口的方式访问 http://pool.sks-keyservers.net:11371

或者直接使用 http 协议提供公钥服务 http://http-keys.gnupg.net

https://tools.ietf.org/html/draft-shaw-openpgp-hkp-00

## 签名

签名文件一般以`.sig`或`.asc`结尾

PGP签名只是检测文件是否完整的一个参考。PGP签名的原理就是使用非对称秘钥加密技术和数字摘要技术产生一段只有文件的原始发布者才能产生的数字串。我们对文件进行PGP签名校验，就是要使用公钥解密文件的原始发布者使用私钥加密的签名，核对上述中的“数字串”是否和原来一致。为了方便获取公钥，人们一般把公钥上传到公钥服务器中。有时候人们为了防止PGP签名本身被伪造，还提供了SHA256用于对PGP签名文件本身的完整性进行检测。

## GPG 配置文件

For GnuPG 1.4 and 2.0 installations this can be used by using the following parameters in gpg.conf:

```
~/.gnupg/gpg.conf:
  keyserver hkps://hkps.pool.sks-keyservers.net
  keyserver-options ca-cert-file=/path/to/CA/sks-keyservers.netCA.pem
```

GnuPG 2.1 users prior to version 2.1.11 (starting with this version the certificate is enabled by default for this pool) want to add the following in dirmngr.conf:

```
~/.gnupg/dirmngr.conf:
   hkp-cacert /path/to/CA/sks-keyservers.netCA.pem
```

## 操作命令

```bash
# GPG 版本号
[shenlin@t460p /usr/local/src]$ gpg --version
gpg (GnuPG) 2.0.22
libgcrypt 1.5.3
Copyright (C) 2013 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Home: ~/.gnupg
Supported algorithms:
Pubkey: RSA, ?, ?, ELG, DSA
Cipher: IDEA, 3DES, CAST5, BLOWFISH, AES, AES192, AES256, TWOFISH,
        CAMELLIA128, CAMELLIA192, CAMELLIA256
Hash: MD5, SHA1, RIPEMD160, SHA256, SHA384, SHA512, SHA224
Compression: Uncompressed, ZIP, ZLIB, BZIP2

# 目录内的软件包和对应的 PGP 签名
[shenlin@t460p /usr/local/src]$ ls
nginx-1.12.2.tar.gz
nginx-1.12.2.tar.gz.asc

# 验证 PGP 签名，提示没有公钥，ID 是 A1C052F8
# 可通过此步获取要去公钥服务器上下载的公钥 ID
[shenlin@t460p /usr/local/src]$ gpg --verify nginx-1.12.2.tar.gz.asc
gpg: Signature made Tue 17 Oct 2017 09:18:21 PM CST using RSA key ID A1C052F8
gpg: Cant check signature: No public key

# 按公钥 ID 在默认的公钥服务器 keys.gnupg.net 上查找，没找到
[shenlin@t460p /usr/local/src]$ gpg --search-keys A1C052F8
gpg: searching for "A1C052F8" from hkp server keys.gnupg.net
gpg: key "A1C052F8" not found on keyserver

# 指定公钥服务器，再次查找
[shenlin@t460p /usr/local/src]$ gpg --keyserver pgp.mit.edu --recv-keys A1C052F8
gpg: requesting key A1C052F8 from hkp server pgp.mit.edu
gpg: key A1C052F8: public key "Maxim Dounin <mdounin@mdounin.ru>" imported
gpg: key A1C052F8: public key "Maxim Dounin <mdounin@mdounin.ru>" imported
gpg: no ultimately trusted keys found
gpg: Total number processed: 2
gpg:               imported: 2  (RSA: 2)

# 查看当前系统中的公钥
[shenlin@t460p ~]$ gpg -k
/home/shenlin/.gnupg/pubring.gpg
--------------------------------
pub   2048R/A1C052F8 2014-06-16 [revoked: 2016-08-16]
uid                  Maxim Dounin <mdounin@mdounin.ru>

pub   2048R/A1C052F8 2011-11-27
uid                  Maxim Dounin <mdounin@mdounin.ru>
sub   2048R/D345AB09 2011-11-27

# 验证签名
[shenlin@t460p /usr/local/src]$ gpg --verify nginx-1.12.2.tar.gz.asc 
gpg: Signature made Tue 17 Oct 2017 09:18:21 PM CST using RSA key ID A1C052F8
gpg: Good signature from "Maxim Dounin <mdounin@mdounin.ru>"        # 完好的签名
gpg: WARNING: This key is not certified with a trusted signature!   # 这把密钥未经受信任的签名认证
gpg:          There is no indication that the signature belongs to the owner.   # 没有迹象表明这个签名属于它所声称的拥有者
Primary key fingerprint: B0F4 2533 73F8 F6F5 10D4  2178 520A 9993 A1C0 52F8

# 生成密钥
[shenlin@t460p ~]$ gpg --gen-key
gpg (GnuPG) 2.0.22; Copyright (C) 2013 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:    # 选择加密算法，默认 RSA
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection? 
RSA keys may be between 1024 and 4096 bits long.    # 选择密钥长度，默认 2048
What keysize do you want? (2048) 
Requested keysize is 2048 bits
Please specify how long the key should be valid.    # 选择密钥过期时间，默认永不过期
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 
Key does not expire at all
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: shenlin                                  # 填写用户信息：姓名，Email，注释
Email address: bitbite@foxmail.com
Comment: hello,world...
You selected this USER-ID:
    "shenlin (hello,world...) <bitbite@foxmail.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
You need a Passphrase to protect your secret key.           # 输入密钥保护密码

We need to generate a lot of random bytes. It is a good idea to perform     # 产生密钥需要生成大量随机字节
some other action (type on the keyboard, move the mouse, utilize the        # 需要用户进行一下操作，如打字，移动鼠标，浏览网页，读写磁盘等
disks) during the prime generation; this gives the random number            # 涉及到的技术：linux 熵池
generator a better chance to gain enough entropy.                           # 熵（entropy）

# 执行到这里就挂起了，原因就是依据上面说明，熵池太浅，需要大量的环境噪音
# 解决方法：安装 rng-tools 填充熵池
# 注意这里无需终止终端运行，打开一个新终端填充熵池完毕后，挂起的操作会继续进行
[shenlin@t460p ~]$ sudo yum install -y rng-tools

# 手动一次性执行填充熵池
[shenlin@t460p ~]$ sudo rngd -r /dev/urandom

# 除了安装 rng-tools 填充熵池，还可以通过磁盘读写来产生熵
[shenlin@t460p ~]$ dd if=/dev/urandom of=/dev/null bs=1024b count=10
10+0 records in
10+0 records out
5242880 bytes (5.2 MB) copied, 0.04122 s, 127 MB/s

# 填充熵池后挂起的操作会立即生成密钥
gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
pub   2048R/7A173015 2018-01-24                                             # 7A173015 是用户ID的hash，可以代替用户ID
      Key fingerprint = E85D C9FC 035A 77AE 3CD0  08AB 5901 22DA 7A17 3015  # 指纹
uid                  shenlin (hello,world...) <bitbite@foxmail.com>
sub   2048R/2419EA7D 2018-01-24                                             # 

# 列出本地密钥 
[shenlin@t460p ~]$ gpg --list-keys
/home/shenlin/.gnupg/pubring.gpg
--------------------------------
pub   2048R/A1C052F8 2014-06-16 [revoked: 2016-08-16]
uid                  Maxim Dounin <mdounin@mdounin.ru>

pub   2048R/A1C052F8 2011-11-27
uid                  Maxim Dounin <mdounin@mdounin.ru>
sub   2048R/D345AB09 2011-11-27

pub   2048R/7A173015 2018-01-24
uid                  shenlin (hello,world...) <bitbite@foxmail.com>
sub   2048R/2419EA7D 2018-01-24
```

上传公钥到密钥服务器
```
[shenlin@t460p ~]$ gpg --send-keys 7A173015 --keyserver hkp://pgp.mit.edu
gpg: "--keyserver" not a key ID: skipping
gpg: "hkp://pgp.mit.edu" not a key ID: skipping
gpg: sending key 7A173015 to hkp server keys.gnupg.net
```
* 密钥服务器是用来发布你的公钥，并将其分发到其他人的服务器
* 这样其他用户可以轻松的根据你数据库中的名字（或者e-mail地址）来获取你的公钥，并给你发送加密信息。
* 避免了把公钥直接拷贝给其他人的过程。

## 生成签名

`-s` 或 `--sign` 生成二进制的签名文件，后缀 .gpg，签名文件较小
`--clearsign` 生成ASCII码文本格式签名文件，后缀 .asc，clearsign 表示 make a clear text signature，签名文件较大
```
[shenlin@t460p ~]$ gpg -s haha.txt
[shenlin@t460p ~]$ gpg --sign haha.txt
You need a passphrase to unlock the secret key for
user: "shenlin (hello,world...) <bitbite@foxmail.com>"
2048-bit RSA key, ID 7A173015, created 2018-01-24

[shenlin@t460p ~]$ gpg --clearsign haha.txt

You need a passphrase to unlock the secret key for
user: "shenlin (hello,world...) <bitbite@foxmail.com>"
2048-bit RSA key, ID 7A173015, created 2018-01-24

[shenlin@t460p ~]$ ll
total 12
-rw-rw-r--. 1 shenlin shenlin  12 Jan 24 20:59 haha.txt
-rw-rw-r--. 1 shenlin shenlin 549 Jan 24 21:01 haha.txt.asc
-rw-rw-r--. 1 shenlin shenlin 337 Jan 24 20:59 haha.txt.gpg

[shenlin@t460p ~]$ cat haha.txt.asc
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA1

123
456
789
-----BEGIN PGP SIGNATURE-----
Version: GnuPG v2.0.22 (GNU/Linux)

iQEcBAEBAgAGBQJaaIObAAoJEFkBItp6FzAVkhEIAJKm6HikvsQmZPa6WoK18U03
PuK2xOGm5xWS5arKweyYCq35laXpBm2gNc7DatlS+3ioytfxr2BvJ/Xt6j5COJrU
rc6HG0qMzwcwgUn6EdclIS2ElL3D83wD+ydqQRf4qmr6YfE/02qoPVBLMJdSPsNd
bZJlDVetygxKYZjCLhDhrEXU1Y1XZr/kcEjHWlS51BhJdpHFpsS6tyKUpH75fmEY
qOvH3sXEV9AWceptIPseSyDJHsskDtdAytTGo7TtdZDpGpeawdrHqqtUqjwQlqK8
n8wdBfBme4ebwAp9ESB5lxZJsFhOzn3rJdCJrLzUX97qZwwj3GxqKOiXO70eLcg=
=KPwt
-----END PGP SIGNATURE-----
```

# 删除密钥

## 加密文件

## 解密文件

3、导出公钥：gpg --export --armor key-id -o file.key

将公钥导出至文件，以便于其他人使用。--armor选项以文本形式显示输出，而非二进制格式。key-id是电子邮箱地址或在--list-keys的pub行中列出的八位十六进制数。

4、导入公钥：gpg --import file.key

从发送给您的密钥文件中导入其他人的公钥

5、加密文件：gpg --encrypt --armor -r key-id file

用key-id的公钥加密消息。如果未提供-r key-id，命令将提示收件人输入。默认输出文件为file.asc.

6、解密文件：gpg --decrypt file

用您的私钥之一解密用公钥加密的消息。
