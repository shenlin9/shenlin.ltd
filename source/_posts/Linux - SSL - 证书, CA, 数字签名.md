---
title: Linux - SSL - 数字证书, CA, 数字签名
categories:
  - Linux
tags:
  - SSL
  - CA
  - 数字证书 certificate
  - 数字签名 signature
---

转载自：
http://www.ruanyifeng.com/blog/2006/12/notes_on_cryptography.html
http://www.ruanyifeng.com/blog/2016/08/migrate-from-http-to-https.html

<!--more-->

## 公钥加密

公钥密码学，即不对称密码学，是一种使用密钥对的加密系统：公钥可能被广泛传播，而私钥只对所有者知道。
与对称密钥算法不同的是，公钥算法不需要一个安全的通道在各方之间进行一次或多次密钥的初始交换。

实现了两个功能：

    身份验证，其中公钥验证了成对的私钥发送消息的持有者，
    加密，只有成对的私钥持有者才能解密使用公钥加密的消息。

加密方法可以分为两大类 :

    1. 单钥加密（private key cryptography，Symmetric encryption，secret-key encryption），也称为对称加密，加密和解密过程都用同一套密码
    2. 双钥加密（public key cryptography，asymmetrical cryptography），也称为公钥加密，不对称加密，加密和解密过程用的是两套密码。

加密算法 :

    1. 通用的单钥加密算法为 DES（Data Encryption Standard），
    2. 通用的双钥加密算法为 RSA（Rivest-Shamir-Adleman），

    一些公钥算法提供密钥分发和保密（例如，迪菲——赫尔曼密钥交换）
    一些提供数字签名（例如 数字签名算法）
    一些两者都提供（例如 RSA）

应用场景 ：

    由于不对称加密的计算复杂性，它通常只用于一小块数据，例如对称加密算法的密钥的传输（例如会话密钥）。
    然后使用这个对称密钥对其余的长消息序列进行加密。
    对称加密/解密基于更简单的算法，而且速度快得多。


> 历史上传统的加密方法都是前一种，比如二战期间德军用的Enigma电报密码。莫尔斯电码也可以看作是一种私钥加密方法。
> 都产生于上个世纪70年代。

在单钥加密的情况下，密钥只有一把，所以密钥的保存变得很重要。一旦密钥泄漏，密码也就被破解。
在双钥加密的情况下，密钥有两把，一把是公开的公钥，还有一把是不公开的私钥。

**在双钥体系中，公钥用来加密信息，私钥用来解密信息和数字签名。**

双钥加密的原理如下：

    a) 公钥和私钥是一一对应的关系，有一把公钥就必然有一把与之对应的、独一无二的私钥，反之亦成立。
    b) 所有的（公钥, 私钥）对都是不同的。
    c) 用公钥可以解开私钥加密的信息，反之亦成立。
    d) 同时生成公钥和私钥应该相对比较容易，但是从公钥推算出私钥，应该是很困难或者是不可能的。

因为任何人都可以生成自己的（公钥，私钥）对，
所以为了防止有人散布伪造的公钥骗取信任，就需要一个可靠的第三方机构来生成经过认证的（公钥，私钥）对。
目前，世界上最主要的数字服务认证商是位于美国加州的 Verisign 公司，它的主要业务就是分发 RSA 数字证书。

## 数字签名

数字签名是用户对自己所独有的数据的“戳”，很难伪造。
数字签名保证对已签名的数据进行任何更改都会被发现。
用户通过私钥和正确的软件，可以将数字签名放在文档和其他数据上。

### Bob 为一个文档签名

1. 把文档数据哈希 hashing，生成只有几行的消息摘要 message digest。
2. 使用自己的私钥加密消息摘要，得到的加密数据就是数字签名 digital signature
3. 把数字签名附加到文档里

### Pat 验证文档的签名

1. 使用Bob的公钥解密签名，将其重新解密为消息摘要。如果成功获得消息摘要，就证明文档确实是 Bob 签名的，因为只有 Bob 才有自己的私钥。

    ??? 这里有个问题，就是判断没有成功获得消息摘要的依据是什么，是公钥错误就解密时报错，还是最后解密出的数据长度格式不对？


    这里涉及到签名方案，如 RSA-PSS （PSS: Provably Secure Encoding Method for Digital Signatures 可验证的数字签名的安全编码方法）

    解密失败提示 "invalid"


    **签名涉及到的算法**

    A digital signature scheme typically consists of 3 algorithms;
    一个数字签名方案通常由3个算法组成;

    1. A key generation algorithm that selects a private key uniformly at random from a set of possible private keys.
    一个密钥的生成算法，负责从一组可能的私有密匙中均匀随机的选择一个私有密匙。

    The algorithm outputs the private key and a corresponding public key.
    该算法输出私钥和相应的公钥。

    2. A signing algorithm that, given a message and a private key, produces a signature.
    一个签名算法，给定一个消息和一个私钥，生成一个签名。

    3. A signature verifying algorithm that, given the message, public key and signature, either accepts or rejects the message's claim to authenticity.
    一个签名验证算法，根据消息，公钥和签名，接受或拒绝消息的真实性。

2. 将文档数据哈希为消息摘要，和签名中解密获得的消息摘要比对，相同则说明文档数据没有被更改。

   这也是为什么要生成摘要再加密成签名，而不是直接使用原文档数据加密成签名，因为明显比对摘要更方便更高效，否则像二进制文件，
   
   包含上百个文件的压缩文件如何比对？

   ??? 这里确认下是人工还是软件比对？

    为什么要生成摘要再加密成签名，而不是直接使用原文档数据加密成签名

    1. 为了提高效率

    The signature will be much shorter and thus save time since hashing is generally much faster than signing in practice.
    签名要短得多，因此节省了时间，因为散列通常比在实际中签名要快得多。
    哈希生成摘要的速度比签名要快的多，

    2. 为了兼容性

    Messages are typically bit strings, but some signature schemes operate on other domains (such as, in the case of RSA, numbers modulo a composite number N). A hash function can be used to convert an arbitrary input into the proper format.
    消息通常是位串，但是一些签名方案在其他域上操作（例如，在RSA中，数字模块化一个复合数字N）。
    哈希函数可以将任意的输入转换成适当的格式。

    3. 为了完整性

    Without the hash function, the text "to be signed" may have to be split (separated) in blocks small enough for the signature scheme to act on them directly.
    如果没有哈希函数，那么“要签名”的文本可能必须被分割为足够小的块，以便签名方案直接对其进行操作。
    However, the receiver of the signed blocks is not able to recognize if all the blocks are present and in the appropriate order.
    但是，签名块的接收者不能识别所有的块是否存在并以适当的顺序出现。

**由上述流程可见数字签名保证了文档不会被更改，人员不会被冒充，即身份验证。**

但上面的流程有一个最重要的前提：Pat 解密时使用的公钥必须确实是 Bob 的，而不是他人的公钥冒充的

如何确保？即 **如何安全的把 Bob 的公钥派发给 Pat**

## CA 和数字证书

CA : certificate authority 或者 certification authority

为了防止有人散布伪造的公钥骗取信任，
就需要一个可靠的第三方机构来生成经过认证的（公钥，私钥）对。
这个机构称为 CA "证书授权中心"（certificate authority），
CA 负责为公钥做认证。
CA 用自己的私钥，对 Bob 的公钥和一些相关信息一起加密，为 Bob 生成了"数字证书"（Digital Certificate）

1. Pat 在收到 Bob 的签名文档之后，会先获取 Bob 的公钥
2. Pat 会先使用 CA 的公钥解密 Bob 数字证书上的 CA 签名，解密成功则证明数字证书是 CA 创建的。
3. 在证书被解除加密之后，Pat 可以检查Bob是否与证书颁发机构保持良好的关系，并且所有关于Bob身份的证书信息都没有被更改。
4. 因为公钥是被 CA 认证过的，所以 Pat 就可以放心的从证书中获取 Bob 的公钥，并使用它来检查Bob的签名。

## 数字证书

公钥证书（public key certificate），也称为数字证书（digital certificate）或身份证明（identity certificate）
是一种用于证明公钥所有权的电子文档，是一个二进制文件，里面包含经过 CA 认证的网站公钥和一些元数据。

证书内容

    公钥信息
    证书所有者信息
    CA 签名

CA 签名有效且 CA 可以信任则就可以与证书所有者安全通信

证书收费形式

    商业证书 ：要从经销商购买，认证级别越高、覆盖范围越广的证书，价格越贵。
    免费证书 ：目前主要是 Let's Encrypt
    自签证书 :

证书分为三种认证级别：

    域名认证（Domain Validation）：最低级别认证，可以确认申请人拥有这个域名。对于这种证书，浏览器会在地址栏显示一把锁。
    公司认证（Company Validation）：确认域名所有人是哪一家公司，证书里面会包含公司信息。
    扩展认证（Extended Validation）：最高级别的认证，浏览器地址栏会显示公司名。

证书分为三种覆盖范围。

    单域名证书：只能用于单一域名，foo.com的证书不能用于www.foo.com
    通配符证书：可以用于某个域名及其所有一级子域名，比如*.foo.com的证书可以用于foo.com，也可以用于www.foo.com
    多域名证书：可以用于多个域名，比如foo.com和bar.com


## CA

CA : Certificate Authority 证书授权中心


1、信息安全传输（A->B,A使用B的公钥加密，B使用B的私钥解密）；
2、数字签名（A->B,A使用A的私钥加密，B使用A的公钥解密）。


谈谈我的理解，其实上面的例子从3个方面保证了信息的安全，信息内容，发信人，收信人。
还是以信件为例，上面的信件来往案例把事情复杂化了，就以Susan给Bob单向发信为例，可以采取这样的方式，分别解决了如下3个问题：
（1）信息内容安全：保证Susan发的信只有Bob能看（发信：Bob公钥加密=》收信：Bob私钥解密,除了Bob其他人看不了）
（2）发信人安全：保证Bob收到的信确实是Susan发的（发信：信件本身使用Bob公钥加密=》收信：Bob私钥解密=》HASH函数得到摘要；数字签名：使用Susan私钥加密=》收到后使用Susan公钥解密=》得到摘要=》两个摘要对比）。核对一致后，Bob保证信一定是Susan发的，因为只有Susan的公钥才能解密数字签名。但Susan发信的时候不一定保证发给了Bob，她不知道用的Bob的公钥是不是正确的。
（3）收信人安全：保证Susan发信一定是给Bob发的，所以通过引入权威的证书机构来发布数字证书，相当于一个公证机构，把大家的公钥搜集到一起进行公证和公示，然后大家去获取这样的数字证书，他们说这个是Bob，那大家都信任这个就是Bob。
总结来说，数字签名就是保证发信人安全的（我签的我认账），数字证书是保证收信人安全的（权威机构说的，他是谁，大家都信）。



我的理解是：
1)要达到保密的目的,则message必须由公钥加密,因为私钥只有一个人有,所以保证只有一个人能解得开。
所以要达到双方的message都是保密的,则须存在两对公私钥,双方互持有自己的私钥和对方的公钥。
但是由于加密的公钥是公开的,所以不能保证这个密文就是特定的某个人如 Bob 加密后给你的,任何持有你的公钥的人都可以给你发密文。

2)因此还需要实现认证这个功能。
这回,Bob使用只有他自己才持有的私钥对message的摘要hash进行加密,然后将之附在原message上发给你。这个时候,你尝试用你手上所有的公钥来对hash密文进行解密,如果某个公钥A解开了hash密文,则证明对该hash进行加密的就一定该公钥所对应的私钥-A。而私钥只有一个人持有,所以能惟一确认这个message是被这个人经手过的。
也就是说,如果我手上的Bob的公钥确实就是属于Bob的话,那么当这个公钥能解开hash密文时,我就能说这个message确实是由Bob发出的。

3)而如何保证"我手上的Bob的公钥确实就是属于Bob"呢？只有引入第三方权威认证。也就是CA了。


