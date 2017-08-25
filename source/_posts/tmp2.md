## 补码

原值取反加 1 得到补码/补数

计算机中的减法运算，其实是使用加法实现的，就是一个原值加上另一个原值的补码

任意一个数和他的补数相加为0

3 - 5 等于 3 + (-5)，从 5 求得 -5 就是使用"取反加1"的方法，5和-5互为补数

http://blog.csdn.net/huangqiang1363/article/details/53449758

## AF PF

AF 表示ADDRESS FAMILY 地址族，PF 表示PROTOCOL FAMILY 协议族，

Winsock2.h中
```
#define AF_INET 0
#define PF_INET AF_INET
```
 
所以在windows中AF_INET与PF_INET完全一样
 
而在Unix/Linux系统中，在不同的版本中这两者有微小差别
对于BSD,是AF,对于POSIX是PF

## Git 迁移后提示证书找不到

```
[2017-08-24 11:46:00] Plugin morhetz/gruvbox
[2017-08-24 11:46:00] $ git clone --recursive "https://github.com/morhetz/gruvbox.git" "C:\Users\ssl\.vim\bundle\gruvbox"
[2017-08-24 11:46:00] > Cloning into 'C:\Users\ssl\.vim\bundle\gruvbox'...
[2017-08-24 11:46:00] > fatal: unable to access 'https://github.com/morhetz/gruvbox.git/': error setting certificate verify locations:
[2017-08-24 11:46:00] >   CAfile: C:/Program Files/Git/mingw32/ssl/certs/ca-bundle.crt
[2017-08-24 11:46:00] >   CApath: none

$ git config --system -l
http.sslcainfo=C:/Program Files/Git/mingw32/ssl/certs/ca-bundle.crt
diff.astextplain.textconv=astextplain

$ git config --system http.sslcainfo C:\cmder\vendor\git-for-windows\mingw32\ssl\certs\ca-bundle.crt

$ git config --system -l
http.sslcainfo=C:\cmder\vendor\git-for-windows\mingw32\ssl\certs\ca-bundle.crt
diff.astextplain.textconv=astextplain
```
