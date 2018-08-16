---
title: MySQL - Manual - 6.4 - 使用加密连接 - Encrypted Connections
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 加密连接介绍

<!--more-->

通过在 MySQL 客户端和服务器之间建立一个未加密的连接，可以访问网络的人可以监视所
有的流量，并检查在客户端和服务器之间发送或接收的数据。

当您必须以安全的方式在网络上移动信息时，未加密的连接是不可接受的。要使任何类型的
数据不可读，使用加密。加密算法必须包括安全元素，以抵抗各种已知的攻击，如更改加密
消息的顺序或重播数据两次。

MySQL 支持在客户端和服务器之间使用 TLS（传输层安全）协议的加密连接。TLS 有时被称
为 SSL（安全套接字层），但是 MySQL 实际上并不使用 SSL 协议来进行加密连接，因为它
的加密很弱（参见 Section 6.4.6,“Encrypted Connection Protocols and Ciphers”）
。

TLS 使用加密算法来确保通过公共网络接收的数据是可信的。它有检测数据变化、丢失或重
放的机制。TLS 还集成了使用 X509 标准提供身份验证的算法。

X509 使得在互联网上识别某人成为可能。在基本术语里，应该有一个称为“证书授权中心
”（或 CA）的实体，它将电子证书分配给任何需要它们的人。证书依赖于具有两个加密密
钥的非对称加密算法（公钥和密钥）。证书所有者可以将证书颁发给另一方作为身份证明。
证书由其所有者的公钥组成。使用此公钥加密的任何数据都只能使用相应的密钥解密，该密
钥由证书所有者持有。

MySQL 可以使用 OpenSSL 或 wolfSSL 编译为支持加密连接。这些包的比较，参考：
Section 6.4.4, “OpenSSL Versus wolfSSL”。

有关每个包支持的加密协议和密码的信息，参考：Section 6.4.6, “Encrypted
Connection Protocols and Ciphers”.

默认情况下，如果服务器支持加密连接，MySQL程序会尝试连接使用加密，如果无法建立加
密连接，则返回到未加密的连接。有关影响使用加密连接的选项的信息参考：
Section 6.4.1, “Configuring MySQL to Use Encrypted Connections”
Section 6.4.2, “Command Options for Encrypted Connections”.

MySQL 在每个连接的基础上执行加密，对给定用户的加密可以是可选的或强制的。这使
您能够根据单个应用程序的需求选择加密或未加密的连接。有关如何要求用户使用加密连接
的信息，请参阅Section 13.7.1.3, “CREATE USER Syntax”中 `CREATE USER` 语句的
`REQUIRE` 子句的讨论。同时参见Section 5.1.7, “Server System Variables”中的
`require_secure_transport` 系统变量的描述。

在主、从复制服务器之间可以使用加密连接，参考：Section 17.3.9,“Setting Up
Replication to Use Encrypted Connections”.

关于 MySQL C API 使用加密连接的信息，参考：Section 27.7.18, “C API Encrypted
Connection Support”.

It is also possible to connect using encryption from within an SSH connection to
the MySQL server host.  
也可以将从 SSH 连接到 MySQL 服务器主机的加密连接起来。例子参考： Section 6.4.7,
“Connecting to MySQL Remotely from Windows with SSH”.

## 6.4.1 Configuring MySQL to Use Encrypted Connections

有几个选项可用来指示是否使用加密连接，并指定适当的证书和密钥文件。

本节提供关于配置服务器和客户端加密连接的一般指导：
* 加密连接的服务器端配置
* 加密连接的客户端配置

关于建立加密连接的完整的选项列表，参考：Section 6.4.2,“Command Options for
Encrypted Connections”.

关于创建所需的证书和密钥文件，参考：Section 6.4.3, “Creating SSL and RSA
Certificates and Keys”.

关于主从复制服务器之间使用加密连接，参考：Section 17.3.9,“Setting Up
Replication to Use Encrypted Connections”.

关于在 MySQL C API 中使用加密连接，参考：Section 27.7.18, “C API Encrypted
Connection Support”.

### Server-Side Configuration for Encrypted Connections

服务器端，`--ssl` 选项指定了服务器允许加密连接，但不是必需，此选项默认启用。

当允许客户端建立加密连接时，服务器端的下列选项确定服务器使用的证书和密钥文件：
* `--ssl-ca`: CA 证书文件的路径名。(`--ssl-capath` 类似但指定的是 CA 证书文件的
  路径名)
* `--ssl-cert`: 服务器公钥证书文件的路径名。此证书可以发送给客户端，并通过客户端
  所拥有的 CA 证书进行身份验证。
* `--ssl-key`: 服务器私钥文件的路径名。

例如，为了使服务器能够进行加密连接，使用 `my.cnf` 文件中这些行启动它，必要时更改
文件名：
```
[mysqld]
ssl-ca=ca.pem
ssl-cert=server-cert.pem
ssl-key=server-key.pem
```
每个选项都以 `PEM` 格式命名一个文件。如果您需要创建所需的证书和密钥文件，请参阅
Section 6.4.3, “Creating SSL and RSA Certificates and Keys”。或者，如果您有一
个 MySQL 源码发行版，您可以使用它的 `mysql-test/std_data` 目录中的演示证书和密钥
文件来测试您的设置。

使用 OpenSSL 编译的 MySQL 服务器可以在启动时自动生成缺失的证书和密钥文件。请参阅
Section 6.4.3.1, “Creating SSL and RSA Certificates and Keys using MySQL”.

服务器执行证书和密钥文件自动发现。如果启用了 `--ssl`（可能还包括 `--ssl-cipher`
）且没有其他 `ssl-xxx` 选项显式地配置加密连接，服务器则试图在启动时自动支持加密
连接：

* 如果服务器在数据目录里发现了名为 `ca.pem`, `server-cert.pem`, `server-key.pem`
  的有效的证书和公钥文件，则启用对客户端加密连接的支持。（这些文件不必是自动生成
  的;重要的是他们有指定的文件名并且是有效的。）

* 服务器在数据目录里没有发现有效的证书和密钥文件，则继续执行但没有加密连接支持。

如果服务器自动启用了对加密连接的支持，它会向错误日志写入一个注释（Note）。

如果服务器发现 CA 证书是自签署的，它就会向错误日志写入一个警告。（如果证书由服
务器自动创建或者手动使用 `mysql_ssl_rsa_setup` 创建，证书就是自签署的。）

服务器使用自动发现和使用的任何证书和密钥文件的名称来设置相应的系统变量（`ssl_ca`
、`ssl_cert`、`ssl_key`）。

为了进一步控制客户端是否必须使用加密连接，请使用 `require_secure_transport` 系统
变量;参见 Section 5.1.7, “Server System Variables”.

为了显式地指定允许的加密协议，使用 `tls_version` 系统变量;参考：Section 6.4.6,“
Encrypted Connection Protocols and Ciphers”.

### Client-Side Configuration for Encrypted Connections

默认情况下，如果服务器支持加密连接，MySQL 客户端程序会尝试建立一个加密连接，通过
`-ssl-mode` 选项可以进一步控制：

* 在没有 `-ssl-mode` 选项的情况下，客户端尝试使用加密连接，如果无法建立加密连接
  ，则返回到未加密的连接。这也是显式使用 `--ssl-mode=PREFFERED` 选项时的行为。

* 使用 `--ssl-mode=REQUIRED` 选项，客户端必需加密的连接，如果无法建立则连接失败。

* 使用 `--ssl-mode=DISABLED` 选项，客户端使用非加密连接。

* 使用 `--ssl-mode=VERIFY_CA` 或 `--ssl-mode=VERIFY_IDENTITY` 选项，客户端必需一
  个加密的连接，而且还分别在证书中对服务器 CA 证书和服务器主机名执行验证。

客户端的下列选项确定了客户端和服务器建立加密连接时使用的证书和密钥文件。它们类似
于服务器端的选项，但用 `--ssl-cert` 和 `--ssl-key` 分别标识客户端公钥和私钥：

* `--ssl-ca`: CA 证书文件的路径名，如果使用了这个选项，则必须指定的是服务器使用
  的相同的证书。（`--ssl-capath` 类似但指定的是 CA 证书文件目录的路径名）

* `--ssl-cert`: 客户端公钥证书文件的路径名

* `--ssl-key`: 客户端私钥文件的路径名

为了获得相对于默认加密提供的额外安全性，客户端可以提供与服务器使用的 CA 证书相匹
配的 CA 证书，并启用主机名身份验证。通过这种方式，服务器和客户端将他们的信任放在
同一个 CA 证书上，而客户端则可以验证它所连接的正是所希望的主机：

* 要指定 CA 证书，使用 `--ssl-ca` 选项(或者 `--ssl-capath`)，并且指定
  `--ssl-mode=VERIFY_CA`

* 要启用主机名身份验证，使用 `--ssl-mode=VERIFY_IDENTITY` 而不是
  `--ssl-mode=VERIFY_CA`

    注意：
    主机名身份验证不支持服务器自动生成的自签名证书或通过 `mysql_ssl_rsa_setup`
    手动生成的证书。
    参考：Section 6.4.3.1, “Creating SSL and RSA Certificates and Keys using
    MySQL”
    这种自签名证书不包含服务器名称作为常用名。

根据客户端使用的 MySQL 帐户的加密要求，客户端可能需要指定某些选项，以便使用加密
连接到支持加密连接的MySQL服务器上。

假设您想要使用一个没有特殊加密需求的帐户进行连接，或者使用包含 `REQUIRE SSL` 选
项的 `CREATE USER` 语句创建的账户进行连接。假设服务器支持加密连接，则客户端加密
连接时可以使用 `-ssl-mode` 选项:
```
mysql
```
或者显式的使用 `--ssl-mode=PREFFERED` 选项：
```
mysql --ssl-mode=PREFERRED
```

对于使用了选项 `REQUIRE SSL` 创建的账户，如果无法建立加密连接，则连接尝试失败。
对于没有特殊加密要求的帐户，如果不能建立加密连接，则返回到未加密的连接。如果无法
获得加密连接，要防止回退和失败，可以像这样连接：
```
mysql --ssl-mode=REQUIRED
```

如果帐户有更严格的安全需求，则必须指定其他选项来建立加密连接：

* 对于使用 `REQUIRE X509` 创建的账户, 客户端必须至少指定 `--ssl-cert` 和
  `--ssl-key`. 另外, `--ssl-ca` (or `--ssl-capath`) 也推荐指定， 以便验证服务器
  提供的公共证书。例如：

```
mysql --ssl-ca=ca.pem \
--ssl-cert=client-cert.pem \
--ssl-key=client-key.pem
```

* 对于 `REQUIRE ISSUER` 或 `REQUIRE SUBJECT` 的账户，要求的选项和 `REQUIRE X509`
  相同，但是证书的 issue 和 subject 必须分别和账户定义里的匹配。

关于 `REQUIRE` 子句的额外信息，参考： Section 13.7.1.3, “CREATE USER Syntax”.

要阻止使用加密连接并覆盖其他的 `--ssl-xxx` 选项，调用客户端程序时使用选项 `--ssl-mode=DISABLED`：
```
mysql --ssl-mode=DISABLED
```

要显式的指定允许的加密协议，使用 `--tls-version` 选项; 参考 Section 6.4.6,“
Encrypted Connection Protocols and Ciphers”.

要确定当前与服务器的连接是否使用了加密，检查状态变量 `Ssl_cipher` 的值，如果值为
空，连接未加密。否则，连接加密了且其值是加密方法名。例如：
```
mysql> SHOW SESSION STATUS LIKE 'Ssl_cipher';
+---------------+---------------------------+
| Variable_name | Value                     |
+---------------+---------------------------+
| Ssl_cipher    | DHE-RSA-AES128-GCM-SHA256 |
+---------------+---------------------------+
```

对于 `mysql` 客户端，一个替代方法是使用 `STATUS` or `\s` 命令，并注意 SSL 行：
```
mysql> \s
...
...
SSL: Not in use
...
...
```
或者：
```
mysql> \s
...
...
SSL: Cipher in use is DHE-RSA-AES128-GCM-SHA256
...
...
```

## 6.4.2 Command Options for Encrypted Connections

本节描述了下列选项：指定是否使用加密连接的选项、指定证书和密钥文件名称的选项以及
与加密连接相关的其他参数。这些选项可以在命令行或选项文件中给出。

建议如何使用的例子以及如何检查连接是否被加密，参考 Section 6.4.1, “
Configuring MySQL to Use Encrypted Connections”.

在 MySQL C API 中使用加密连接的信息，参考 Section 27.7.18, “C API Encrypted
Connection Support”.

### 加密连接选项摘要

`--skip-ssl`        不使用加密连接
`--ssl`             启用加密连接
`--ssl-ca`          包含了可信的 SSL 证书颁发机构列表的文件
`--ssl-capath`      包含了可信的 SSL 证书颁发机构证书文件的目录
`--ssl-cert`        包含了 X509 证书
`--ssl-cipher`      连接加密的许可密码列表
`--ssl-crl`         包含证书撤销列表的文件
`--ssl-crlpath`     包含证书撤销列表的文件的目录
`--ssl-fips-mode`   是否在客户端 8.0.11 启用 FIPS 模式
`--ssl-key`         包含 X509 密钥的文件
`--ssl-mode`        连接到服务器的安全状态
`--tls-version`     加密连接允许的协议

### 加密连接选项详细

#### `--ssl`

注意：客户端 `--ssl` 选项从 MySQL 8.0 里移除了. 客户端程序可使用 `--ssl-mode` 代
替.

在服务器端，`--ssl` 选项指定了服务器允许加密连接，但不要求必须是加密连接。此选项
在服务器端默认启用。

`--ssl` 是被其他的 `--ssl-xxx` 选项隐含的。

否定形式的 `--ssl` 选项表明不应使用加密连接，且会覆盖其他的 `--ssl-xxx` 选项。否
定形式为： `--ssl=0` 或同义词 (`--skip-ssl`, `--disable-ssl`).

为加密连接指定额外的参数，至少在服务器端使用 `--ssl-cert` 和 `--ssl-key`，在客户
端使用 `--ssl-ca`. 参考 Section 6.4.1, “Configuring MySQL to Use Encrypted
Connections”. 此节还描述了自动生成和自动发现证书和密钥文件的服务器功能。

#### `--ssl-ca=file_name`

PEM 格式的 CA 证书文件的路径名，用于服务器端时此选项隐含：`--ssl`

要告诉客户端在建立到服务器的加密连接时不要对服务器证书进行身份验证，请不要指定
`--ssl-ca` 或 `--ssl-capath` 选项。服务器仍然根据为客户端帐户建立的任何适用的需
求来验证客户端，并且仍然使用在服务器端指定的任何 `--ssl-ca` 或 `ssl-capath` 选项
值。

#### `--ssl-capath=dir_name`

包含了 PEM 格式的 SSL CA 证书文件的目录的路径名，用于服务器端时此选项隐含：`--ssl`

要告诉客户端在建立到服务器的加密连接时不要对服务器证书进行身份验证，请不要指定
`--ssl-ca` 或 `--ssl-capath` 选项。服务器仍然根据为客户端帐户建立的任何适用的需
求来验证客户端，并且仍然使用在服务器端指定的任何 `--ssl-ca` 或 `ssl-capath` 选项
值。

使用 OpenSSL 编译的 MySQL 发行版支持 `--ssl-capath` 选项 (参考 Section 6.4.4,“
OpenSSL Versus wolfSSL”). 而使用 wolfSSL 编译的发行版则不支持，这是因为 wolfSSL
不搜索任何目录也不跟踪链式证书树。wolfSSL 要求 CA 证书树的所有组件都被包含在一个
单独的 CA 证书树里，且文件里的每个这个证书都有唯一的 `SubjectName` 值。为了解决
wolfSSL 的此限制，将包含证书树的单个证书文件连接到一个新文件中，并将该文件指定为
`--ssl-ca` 选项的值。

#### `--ssl-cert=file_name`

PEM 格式的 SSL 公钥证书文件的路径名，用于客户端时，是客户端公钥证书，用于服务器
端时，是服务器的公钥证书。用于服务器端时，其隐含了 `--ssl` 选项。

#### `--ssl-cipher=cipher_list`

连接加密允许的加密算法列表，如果列表里没有支持的加密算法，则加密连接无法工作。用
于服务器端时，其隐含了 `--ssl` 选项。

为了获得最大的可移植性，`cipher_list` 应该是一个或多个密码算法名的列表，由冒号隔
开。例子:
```
--ssl-cipher=AES128-SHA
--ssl-cipher=DHE-RSA-AES128-GCM-SHA256:AES128-SHA
```

指定密码算法时 OpenSSL 支持一种更灵活的语法，参考文档：
https://www.openssl.org/docs/manmaster/man1/ciphers.html. wolfSSL 则不支持, 所以
对使用 wolfSSL 编译的 MySQL 发行版使用此扩展语法就会失败。

关于 MySQL 支持的加密算法信息，参考 Section 6.4.6, “Encrypted Connection
Protocols and Ciphers”.

#### `--ssl-crl=file_name`

PEM 格式的证书撤销列表文件的路径名，用于服务器端时，隐含 `--ssl` 选项。

如果没有 `-ssl-crl` 或 `ssl-crlpath`，即使 CA 路径包含证书撤销列表，也不会执行
CRL 检查。

使用 OpenSSL 编译的 MySQL 发行版支持 `--ssl-crl` 选项，(see Section 6.4.4,“
OpenSSL Versus wolfSSL”). 使用 wolfSSL 编译的 MySQL 发行版则**不**支持
`--ssl-crl` 选项，因为撤销列表不适用于 wolfSSL。

#### `--ssl-crlpath=dir_name`

包含了 PEM 格式的证书撤销列表文件的目录路径名，用于服务器端时，隐含 `--ssl` 选项。

如果没有 `-ssl-crl` 或 `ssl-crlpath`，即使 CA 路径包含证书撤销列表，也不会执行
CRL 检查。

使用 OpenSSL 编译的 MySQL 发行版支持 `--ssl-crlpath` 选项，(see Section 6.4.4,“
OpenSSL Versus wolfSSL”). 使用 wolfSSL 编译的 MySQL 发行版则**不**支持
`--ssl-crlpath` 选项，因为撤销列表不适用于 wolfSSL。

#### `--ssl-fips-mode={OFF|ON|STRICT}`

控制是否在客户端启用 FIPS 模式，`--ssl-fips-mode` 不同于其他 `--ssl-xxx` 选项，
它不是用于建立加密连接的，而是影响哪些加密操作是允许的。参考 Section 6.6, “FIPS
Support”.

`--ssl-fips-mode` 允许下列值：

* OFF: 禁用 FIPS 模式
* ON: 启用 FIPS 模式
* STRICT: 启用 “strict” FIPS 模式

注意：如果 OpenSSL FIPS 对象模型不可用，则唯一允许的值是 `OFF`。这种情况下，如果
设置为 `ON` 或 `STRICT`，则客户端启动时会生成一个警告并且在 `non-FIPS` 模式下操作。

#### `--ssl-key=file_name`

PEM 格式的 SSL 私钥文件的路径名。

用于客户端时，指示客户端的私钥。用于服务器端时，指示服务器端的私钥。

用于服务器端时，隐含了 `--ssl` 选项。

如果密钥文件被使用密码保护了，则程序会提示用户输入密码，密码必须以交互式的给出，
而不能被存储在文件里。如果密码不正确，程序继续运行就像它无法读取密钥一样。

为了获得更好的安全性，请使用至少 2048 位的 RSA 密钥证书。

#### `--ssl-mode=mode`

这个选项只适用于客户端程序，不适用服务器。它指定连接到服务器的安全状态。

下列选项值是允许的：

* `PREFERRED`: 如果服务器支持加密连接则建立一个加密的连接，如果不能建立加密连接
  则回退回建立非加密连接。这是未指定 `--ssl-mode` 的默认值。

* `REQUIRED`: 如果服务器支持加密连接则建立一个加密的连接，如果不能建立加密连接则
  连接失败。

* `VERIFY_CA`: 类似 `REQUIRED`，但额外的还要根据配置的 CA 证书验证服务器的 CA 证书。如
  果没有找到有效匹配的 CA 证书，则连接尝试失败。

* `VERIFY_IDENTITY`: 类似 `VERIFY_CA`，但额外的还要通过检查客户端用来连接到服务
  器的主机名 和 服务器发送给客户机的证书中的标识，来执行主机名身份验证。

  * As of MySQL 8.0.12, if the client uses OpenSSL 1.0.2 or higher, the client checks whether the host name that it uses for connecting matches either the Subject Alternative Name value or the Common Name value in the server certificate.
  * 从 MySQL 8.0.12 起，如果客户端使用 OpenSSL 1.0.2 或更高版本，客户端将检查它用于连接的主机名是否匹配主题替代名（Subject Alternative Name value）或服务器证书中的公共名称值。

  * Otherwise, the client checks whether the host name that it uses for connecting matches the Common Name value in the server certificate.
  * 否则，客户端将检查它用来连接的主机名是否与服务器证书中的公用名称值相匹配。

  如果不匹配，连接失败。对于加密连接，这个选项有助于防止中间人攻击。

  注意：
  主机名标识验证与由服务器自动创建的自签名证书不兼容，与使用
  `mysql_ssl_rsa_setup` 手动创建的自签名证书也不兼容。(see Section 6.4.3.1, “
  Creating SSL and RSA Certificates and Keys using MySQL”)。这种自签名证书不包
  含服务器名称作为公用名称值。

* `DISABLED`: 建立一个不加密的链接

`--ssl-mode` 选项和 CA 证书选项之间的交互如下：

* 如果没有显式的设置 `--ssl-mode`，则使用 `--ssl-ca` 或 `--ssl-capath` 时则隐含
  着 `--ssl-mode=VERIFY_CA`

* 如果 `--ssl-mode` 值为 `VERIFY_CA` 或 `VERIFY_IDENTITY`, 则仍需要 `--ssl-ca`
  或 `--ssl-capath` 以提供与服务器使用的 CA 证书相匹配的 CA 证书。

* An explicit `--ssl-mode` option with a value other than `VERIFY_CA` or `VERIFY_IDENTITY`, together with an explicit `--ssl-ca` or `--ssl-capath` option, produces a warning that no verification of the server certificate will be done, despite a CA certificate option being specified.

To require use of encrypted connections by a MySQL account, use CREATE USER to create the account with a REQUIRE SSL clause, or use ALTER USER for an existing account to add a REQUIRE SSL clause. Connection attempts by clients that use the account will be rejected unless MySQL supports encrypted connections and an encrypted connection can be established.

The REQUIRE clause permits other encryption-related options, which can be used to enforce security requirements stricter than REQUIRE SSL. For additional details about which command options may or must be specified by clients that connect using accounts configured using the various REQUIRE options, see the description of REQUIRE in Section 13.7.1.3, “CREATE USER Syntax”.

#### ` --tls-version=protocol_list`

用于客户端程序，表示加密连接时客户端允许的协议，值是使用逗号分隔的协议名列表，例
如：

```
mysql --tls-version="TLSv1.1,TLSv1.2"
```

可用于此选项的协议名取决于编译 MySQL 时使用的 SSL 库，详细参考 Section 6.4.6, “
Encrypted Connection Protocols and Ciphers”.

服务器端使用系统变量 `tls_version ` 代替。

## 6.4.3 Creating SSL and RSA Certificates and Keys

下面的讨论描述了如何在 MySQL 中创建 SSL 和 RSA 支持所需的文件。

可以使用 MySQL 本身提供的便利来创建文件，也可以直接调用 openssl 命令。

SSL 证书和密钥文件使 MySQL 能够使用 SSL 支持加密连接。
See Section 6.4.1, “Configuring MySQL to Use Encrypted Connections”.

RSA 密钥文件使 MySQL 能够为经过 `sha256_password` 或 `caching_sha2_password` 插
件验证的帐户通过不加密的链接安全的进行密码交换提供支持。
See Section 6.5.1.2, “SHA-256 Pluggable Authentication”, and Section 6.5.1.3,
“Caching SHA-2 Pluggable Authentication”.

### 6.4.3.1 Creating SSL and RSA Certificates and Keys using MySQL

MySQL 提供了这些方法来创建 SSL 证书、密钥文件和 RSA 密钥对文件，使用 SSL 来支持
加密连接和使用 RSA 在未加密的连接上进行安全密码交换时需要这些文件，如果这些文件
丢失了：

* 对于使用 OpenSSL 编译的 MySQL 发行版，服务器可以在启动时自动生成这些文件。

* 用户可以手动调用实用程序 `mysql_ssl_rsa_setup` 生成文件

* 对于一些发行版类型，如 RPM 包，`mysql_ssl_rsa_setup` 调用发生在数据目录初始化
  时。这种情况下，只要 openssl 命令可用，MySQL 发行版不需要是使用 OpenSSL 编译过
  的。

  重要：
  服务器自动生成文件和通过使用 `mysql_ssl_rsa_setup` 使得生成所需文件更容易，从而降低了使用 SSL 的障碍。然而，这些方法生成的证书是自签名的，这可能不是很安全。在您获得使用这些文件的经验之后，可以考虑从注册的 CA 处获得证书/密钥材料。

#### Automatic SSL and RSA File Generation

对于使用 OpenSSL 编译的 MySQL 发行版，MySQL 可以在启动时自动生成缺失的 SSL 和
RSA 文件，使用系统变量 `auto_generate_certs`,
`sha256_password_auto_generate_rsa_keys`,
`caching_sha2_password_auto_generate_rsa_keys` 控制自动生成文件。所有变量默认都
是启用的，这些变量可以在启动时启用，但不能在运行时设置。They can be enabled at
startup and inspected but not set at runtime.

如果启用了 `auto_generate_certs` 系统变量，除了 `--ssl` 之外没有指定其他 SSL 选
项，并且服务器端数据目录中 SSL 文件缺失，服务器在启动时会在数据目录里自动生成服
务器端和客户端的 SSL 证书和密钥文件。这些文件启用了使用 SSL 加密的客户机连接。
see Section 6.4.1, “Configuring MySQL to Use Encrypted Connections”.

1. 服务器在数据目录中通过以下名称检查 SSL 文件：
```
ca.pem
server-cert.pem
server-key.pem
```

2. 如果这些文件中有任何一个，服务器就不会创建 SSL 文件。否则会创建它们，再加上一
   些额外的文件：
```
ca.pem Self-signed CA certificate
ca-key.pem CA private key
server-cert.pem Server certificate
server-key.pem Server private key
client-cert.pem Client certificate
client-key.pem Client private key
```

3. 如果服务器自动生成了 SSL 文件，则分别使用文件 `ca.pem`, `server-cert.pem`,
   `server-key.pem` 来设置对应的系统变量 `ssl_ca`, `ssl_cert`, `ssl_key`。

如果下列的条件全部为真，则服务器在启动时自动在数据目录里生成 RSA 私钥/公钥密钥对文件：
* 启用了系统变量 `sha256_password_auto_generate_rsa_keys` 或
  `caching_sha2_password_auto_generate_rsa_keys`
* 没有指定 RSA 选项
* 数据目录里缺失 RSA 文件
这些密钥对文件使得通过插件 `sha256_password ` 或 `caching_sha2_password ` 验证的
账户可以在未加密的链接上使用 RSA 进行安全密码交换，参考 Section 6.5.1.2, “
SHA-256 Pluggable Authentication”, and Section 6.5.1.3, “Caching SHA-2
Pluggable Authentication”.

1. 服务器使用下列名字在数据目录里检测 RSA 文件：
```
private_key.pem
public_key.pem
```
2. 如果数据目录里有上述文件中有任何一个，服务器就不会创建 RSA 文件。否则就创建。

3. 如果服务器自动生成了 RSA 文件，则使用它们的名字来设置对应的系统变量
   (`sha256_password_private_key_path`,`sha256_password_public_key_path`;
   `caching_sha2_password_private_key_path`,`caching_sha2_password_public_key_path`).

#### Manual SSL and RSA File Generation Using mysql_ssl_rsa_setup

MySQL 发行版包含了一个实用程序 `mysql_ssl_rsa_setup`，可以手动调用来生成 SSL 和
RSA 文件。 所有的 MySQL 发行版（无论是使用 OpenSSL 编译的还是使用 wolfSSL 编译的
）里都包含了这个实用程序，不过它要求 openssl 命令必须可用。使用说明参考 Section
4.4.3, “mysql_ssl_rsa_setup — Create SSL/RSA Files”.

#### SSL and RSA File Characteristics

服务器自动生成的或者是通过调用 `mysql_ssl_rsa_setup` 生成的 SSL 和 RSA 文件有下
列特性：
* SSL 和 RSA 密钥的大小为 2048 位。
* SSL CA 证书是自签署的
* 使用 CA 证书和密钥签署 SSL 服务器和客户端证书，使用的是
  `sha256WithRSAEncryption` 签名算法。
* SSL 证书使用这些公用名称（Common Name, CN）值，并使用适当的证书类型（CA、服务
  器、客户机）：
    ```
    ca.pem:             MySQL_Server_suffix_Auto_Generated_CA_Certificate
    server-cert.pm:     MySQL_Server_suffix_Auto_Generated_Server_Certificate
    client-cert.pm:     MySQL_Server_suffix_Auto_Generated_Client_Certificate
    ```

  后缀值是基于 MySQL 版本号的。对于由 `mysql_ssl_rsa_setup` 生成的文件，可以使用
  `--suffix` 选项显式地指定后缀。

  对于由服务器生成的文件，如果产生的 CN 值超过 64 个字符，则省略名称的 `_suffix`
  部分。

* SSL files have blank values for Country (C), State or Province (ST),
  Organization (O), Organization Unit Name (OU) and email address.

* 由服务器或 `mysql_ssl_rsa_setup` 创建的 SSL 文件从生成时起有效期 10 年。
* RSA 文件不会过期。
* 对于每个证书/密钥对 SSL 文件有不同的序列号（CA 1，服务器 2，客户端 3）
  由服务器自动创建的文件由运行服务器的账户拥有。使用 `mysql_ssl_rsa_setup` 创建
  的文件由调用该程序的用户拥有。如果该程序是由 `root` 调用的，并且使用了 `-uid`
  选项用来指定哪个用户应该拥有这些文件，那么在支持 `chown()` 系统调用的系统上可
  以改变这一点。
* 在 Unix 和类 Unix 系统上，用于证书文件的文件访问模式是 644（即世界可读的），用
  于密钥文件的文件访问模式是 600（也就是说，只有运行服务器的账户才能访问）。

要查看 SSL 证书的内容（例如，检查其有效日期的范围），直接调用 openssl：
```
openssl x509 -text -in ca.pem
openssl x509 -text -in server-cert.pem
openssl x509 -text -in client-cert.pem
```

也可以使用这个 SQL 语句检查 SSL 证书过期信息：
```
mysql> SHOW STATUS LIKE 'Ssl_server_not%';
+-----------------------+--------------------------+
| Variable_name | Value |
+-----------------------+--------------------------+
| Ssl_server_not_after | Apr 28 14:16:39 2027 GMT |
| Ssl_server_not_before | May 1 14:16:39 2017 GMT |
+-----------------------+--------------------------+
```

### 6.4.3.2 Creating SSL Certificates and Keys Using openssl




### 6.4.3.3 Creating RSA Keys Using openssl




## 6.4.4 OpenSSL Versus wolfSSL




## 6.4.5 Building MySQL with Support for Encrypted Connections



## 6.4.6 Encrypted Connection Protocols and Ciphers



## 6.4.7 Connecting to MySQL Remotely from Windows with SSH



