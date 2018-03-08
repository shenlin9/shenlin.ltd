---
title: PHP - 字符串函数
categories:
  - PHP
tags:
  - 字符串函数
---

字符串函数

<!--more-->

## 数组相关

### implode

```php 
string implode(string $glue, array $pieces)
string implode(array $pieces)
```

把数组`$pieces`使用字符串`$glue`连接为字符串 或 直接连接为字符串

只针对一维数组
由于历史原因，`$glue`可省略
关联数组只连接value不连接key
`join` — 别名 `implode`

### explode

```php
array explode ( string $delimiter , string $string [, int $limit ] )
```

使用字符串`$demimiter`分割字符串`$string`得到数组


### str_split

```php
array str_split ( string $string [, int $split_length = 1 ] )
```

将字符串`$string`按指定长度`$split_length`切割为数组


## 针对特定格式字符串

### parse_str

```php
void parse_str ( string $str [, array &$arr ] )
```

将URL中的QueryString字符串直接解析成变量 或 解析到数组`&$arr`中

### str_getcsv

```php
array str_getcsv ( string $input[, string $delimiter = "," [, string $enclosure = '"' [, string $escape = "\\" ]]] )
```

解析 CSV 字符串为一个数组

## 切割字符串

### strtok

```php
string strtok ( string $str , string $token )
string strtok ( string $token )
```

分割字符串`$str`使用`$token`标记中的每一个字符，第一次调用需要指明`$str`，以后每次调用只需指明`$token`，每分割一次获得一个字符串

### chunk_split

```php
string chunk_split ( string $body [, int $chunklen = 76 [, string $end = "\r\n" ]] )
```

将字符串 `$body` 格式为每隔指定长度 `$chunklen` 就添加 `$end` 字符的字符串

严格按照 `$chunklen` 长度，单词将被切割，中文将乱码

`$chunklen` 默认值76，和 `base64_encode()` 配合输出符合RFC2045多用途网际邮件扩充协议(MIME)的字符串

### wordwrap

```php
string wordwrap ( string $str [, int $width = 75 [, string $break = "\n" [, bool $cut = false ]]] )
```

将字符串 `$str` 格式化为指定长度 `$width` 就添加 `$break` 的字符串，`$cut` 指明是否切割单词

## 查找指定字符串位置

### strpos

```php
mixed strpos ( string $haystack , mixed $needle [, int $offset = 0 ] )
``` 
stripos
strpos 函数的忽略大小写版本

在字符串 `$haystack` 中查找字符串 `$needle` **第一次** 出现的位置
方向永远是向后查找
`$offset` 为正表示从`$offset`位置开始向后查找
`$offset` 为负表示从倒数第`$offset`个字符开始向后查找


```php
int strrpos ( string $haystack , string $needle [, int $offset = 0 ] )

strripos strrpos 函数的忽略大小写版本
```

在字符串 `$haystack` 中查找字符串 `$needle` **最后一次** 出现的位置
方向永远是向后查找
`$offset` 为正表示从`$offset`位置开始向后查找
`$offset`为负表示从0开始`向后查找，但查`找截止位置为倒数第`$offset`个字符


## 查找指定字符串位置并返回字符串

### strstr

```php
string strstr ( string $haystack , mixed $needle [, bool $before_needle = false ] )

stristr strstr函数的忽略大小写版本

strchr — 别名 strstr
```

在字符串`$haystack`中找到字符串`$needle` **首次出现** 的位置，
并返回自`$needle`位置到`$haystack`末尾的字符串（包含`$needle`），
`$before_needle`为true则返回`$needle`位置之前的字符串（不包含`$needle`）

### strrchr
 
```php
string strrchr ( string $haystack , mixed $needle )
```

在字符串`$haystack`中找到字符串`$needle` **最后出现**的位置，
并返回自`$needle`位置到`$haystack`末尾的字符串（包含`$needle`）
> 和`strstr()`不同，这里的`$needle`必须为字符，如果是字符串则只取第一个字符

### strpbrk

```php
string strpbrk ( string $haystack , string $char_list )
```

在字符串`$haystack`中找到字符列表`$char_list`中任意单个字符**首次匹配**的位置，
并返回自此位置到`$haystack`末尾的字符串（包含那个匹配的字符）

### substr

```php
string substr ( string $string , int $start [, int $length ] )
```

在字符串`$string`中根据开始位置`$start`和长度`$length`返回字符串
`$start`为非负数则表示开始位置的偏移量，为负数则表示倒数第`$start`个字符
`$length`省略则取到末尾，为正数则表示获取字符的长度，为负数则表示截止到倒数第`$length`个字符为止

## 字符串替换

### substr_replace

```php
mixed substr_replace ( mixed $string , mixed $replacement , mixed $start [, mixed $length ] )
```
在`$string`中用`$replacement`字符串替换从`$start`开始`$length`长度的字符串

`$string`
可以为字符串也可以为数组，则返回结果也分别是字符串和数组

`$replacement`
可以为字符串也可以为数组，为空""时表示删除

`$start`
可为整数也可为数组
正数表示从0开始的偏移量，负数表示从倒数第`$start`个字符开始
数组表示按对应关系指定每次替换的开始位置

`$length`
可为整数也可为数组
省略表示整个字符串长度`strlen($string)`
0表示在`$start`的位置插入
正数表示长度，负数表示到倒数第`$length`个截止
数组表示按对应关系指定每次替换的长度

### str_replace

```php
mixed str_replace ( mixed $needle , mixed $replace , mixed $haystack [, int &$count ] )

str_ireplace str_replace 的忽略大小写版本
```

找到`$needle`并替换为`$replace`在目标`$haystack`中，可以通过引用传递的参数`$count`接收替换次数

参数为数组时，会按数组元素顺序从左到右执行多次替换

> `$needle` `$replace`

都可为字符串或数组

`$needle`为数组，`$replace`为字符串，则表示把`$haystack`里包含的`$needle`中所有元素都替换为`$replace`的字符串

`$needle`为数组，`$replace`为数组，但`$replace`元素数量较少，则使用空字符串替换`$needle`中多余的元素

> `$haystack`

可为字符串或数组，对应的返回结果也是字符串和数组

> `&$count`

此参数通过引用传递可以接收替换执行的次数

### strtr

```php
string strtr ( string $str , string $from , string $to )
```

把字符串`$str`中的字符`$from`按字节对应替换为`$to`，如`echo strtr('abcba','ab','01')`将输出 01c10

是**字节替换**，`$from`和`$to`长度不同时，多余的字符将被忽略，将按最短的参数进行替换

### strtr

```php
string strtr ( string $str , array $replace_pairs )
```

把字符串`$str`按数组`$replace_pairs`中key和value的对应关系进行字符串替换，如`echo strtr('abcba',['ab'=>'01'])`将输出 01cba

是**字符串替换**，key和value长度可不同


## 字符串输出语言结构

### echo

```php
void echo ( string $arg1 [, string $... ] )
```

是一个语言结构

调用时参数无需括号
 
参数为字符串，可以有多个参数

多个参数时必须 **不** 加括号

快捷语法`<?=$var?>`必须启用`short_open_tag`

不能被可变函数直接调用
```php
$func = "echo";
$func('aaa');
//提示 Call to undefined function
```

要调用需要再封装
```php
function echoit($arg)
{
    echo $arg;
}

$func = "echoit";
$func('aaa');
```

没有返回值，表现不像函数，不能用于需要函数的场合，如：
```php
var?echo true:echo false;
```

### print
```php
int print ( string $arg )
```
是一个语言结构
参数无需括号
不能被可变函数调用
返回值总是1，表现像函数：
```php
$var = '';
var_dump($var?print true:print false);

//输出
int(1)
```

## 格式化输出或返回

> 
函数名中所含字符含义：
> 
后f 按`$format`格式化输出
前f 文件 handle 句柄打开的流
v 表示参数为一个数组，否则为多个字符串参数
s 表示不输出而是返回字符串，否则直接输出字符串返回字符串长度

### printf

```php
int printf ( string $format [, mixed $args [, mixed $... ]] )
```
 
依据 format 参数直接输出字符串。

参数为format中可以处理的类型，可以有多个参数

返回的是输出字符串的长度。

`$format`格式详解：

|%|position specifier|sign specifier|padding specifier|alignment specifier|width specifier|precision specifier|type specifier|
|-||||||||
|%|n\$|+|space/0/'x|-|m|.n|s/d/f/...|
||第n参数，和arg参数个数一一对应|是否显示正数的正号|指定填充字符|对齐方式|输出结果最少要多少个字符|浮点数表示小数点后的位数|类型|
||使用位置指定后，无须一一对应|默认只有负数才显示前面的负号-，+会强制正数也显示前面的+号|默认空格填充，0填充不需要使用'，其他字符需用'指定填充字符|默认右对齐，-表示左对齐|只有在达不到这里指定的字符数时才使用前面的padding specifier填充|字符串则表示最多有多少字符|常用：s,d,f|

只有 % 和 type specifier 是必选的，其他都是可选项。
type specifier说明：
    
    % 输出%本身，所以要输出%c则应该写%%c
    
    c 按ASCII编码输出对应的字母
    s 输出字符串
    
    d 输出有符号整型数
    u 输出无符号整型数
    e 输出科学计数法表示的数字，其中的e为小写，如1e+3
    E 输出科学计数法表示的数字，其中的E为大写，如1E+3
    f 输出浮点数
    F 输出浮点数，不区分语言地区
    
    b 输出2进制数据
    o 输出8进制数据
    x 输出16进制数据，a-f为小写
    X 输出16进制数据，A-F为大写

### sprintf

```php
string sprintf ( string $format [, mixed $args [, mixed $... ]] )
```

同printf，只是不直接输出生成的字符串，而是返回。

### vprintf

```php
int vprintf ( string $format , array $args )
```

依据 format 参数直接输出字符串。

参数为数组，且只有一个

返回的是输出字符串的长度。

### vsprintf

```php
string vsprintf ( string $format , array $args )
```
 
依据 format 参数生成字符串并返回。 

参数为数组，且只有一个

返回的是最终生成的字符串。
    
### fprintf

```php
int fprintf ( resource $handle , string $format [, mixed $args [, mixed $... ]] )
``` 
按format格式化字符串并写到由handle句柄打开的流中。 

$handler    文件系统指针，是典型的由fopen()创建的resource

返回写入的字符串长度。 

```php
if(!($fp=fopen('date.txt','w'))){
    return;
}

fprintf($fp,'%04d-%02d-%02d',$year,$month,$day);
```

### vfprintf

```php
int vfprintf ( resource $handle , string $format , array $args )
```
同上面的fprintf，只是参数为数组


### 解析输入字符串

```
mixed sscanf ( string $str , string $format [, mixed &$... ] )
```

```
mixed fscanf ( resource $handle , string $format [, mixed &$... ] )
```

trim — 去除字符串首尾处的空白字符（或者其他字符）
ltrim — 删除字符串开头的空白字符（或其他字符）
rtrim — 删除字符串末端的空白字符（或者其他字符）
chop — rtrim 的别名

lcfirst — 使一个字符串的第一个字符小写
ucfirst — 将字符串的首字母转换为大写
ucwords — 将字符串中每个单词的首字母转换为大写
strtolower — 将字符串转化为小写
strtoupper — 将字符串转化为大写

money_format — 将数字格式化成货币字符串
number_format — 以千位分隔符方式格式化一个数字


chr — 返回指定的字符
ord — 返回字符的 ASCII 码值
strlen — 获取字符串长度
strrev — 反转字符串
str_shuffle — 随机打乱一个字符串
str_repeat — 重复一个字符串
str_pad — 使用另一个字符串填充字符串为指定长度


substr_count — 计算字串出现的次数
count_chars — 返回字符串所用字符的信息
str_word_count — 返回字符串中单词的使用情况
strspn — 计算字符串中全部字符都存在于指定字符集合中的第一段子串的长度。
strcspn — 获取不匹配遮罩的起始子字符串的长度


nl2br — 在字符串所有新行之前插入 HTML 换行标记，其实相当于用<br>替换\n
strip_tags — 从字符串中去除 HTML 和 PHP 标记
htmlentities — 将字符转换为 HTML 转义字符
html_entity_decode — Convert all HTML entities to their applicable characters
htmlspecialchars — 将特殊字符转换为 HTML 实体
htmlspecialchars_decode — 将特殊的 HTML 实体转换回普通字符
get_html_translation_table — 返回使用 htmlspecialchars 和 htmlentities 后的转换表

应该把所有进制相关函数整理在一起
-----------------------------------------------------------
bin2hex — 函数把包含数据的二进制字符串转换为十六进制值
hex2bin — 转换十六进制字符串为二进制字符串
-----------------------------------------------------------

crc32 — 计算一个字符串的 crc32 多项式，常用于检查数据的完整性
md5 — 计算字符串的 MD5 散列值
md5_file — 计算指定文件的 MD5 散列值
sha1 — 计算字符串的 sha1 散列值
sha1_file — 计算文件的 sha1 散列值
crypt — 单向字符串散列


strcoll — 基于区域设置的字符串比较
strcmp — 二进制安全字符串比较
    strcasecmp — 二进制安全比较字符串（不区分大小写）
strncmp — 二进制安全比较字符串，可以指定比较的长度
    strncasecmp — 二进制安全比较字符串开头的若干个字符（不区分大小写）
strnatcmp — 使用自然排序算法比较字符串，如strcmp的标准比较：1<10<11<2，而strnatcmp的自然比较：1<2<10<11
    strnatcasecmp — 使用“自然顺序”算法比较字符串（不区分大小写）
substr_compare — 二进制安全比较字符串（从偏移位置比较指定长度）




addslashes — 使用反斜线引用字符串，用于数据库插入，但应优先使用数据库自己的转义函数
addcslashes — 以 C 语言风格使用反斜线转义字符串中的字符
stripslashes — 反引用一个引用字符串
stripcslashes — 反引用一个使用 addcslashes 转义的字符串


国外语言相关
------------------------------
hebrev — 将逻辑顺序希伯来文（logical-Hebrew）转换为视觉顺序希伯来文（visual-Hebrew）
hebrevc — 将逻辑顺序希伯来文（logical-Hebrew）转换为视觉顺序希伯来文（visual-Hebrew），并且转换换行符
        鉴于希伯来语是由右向左书写，故ISO-8859-8有视觉顺序(Visual order) 与逻辑顺序(Logical order)之分。
        "视觉顺序"即不理会希伯来语字母的排列， 按照西欧语言习惯由左至右记录， 而"逻辑顺序"则以希伯来语使用者的习惯记录。
convert_cyr_string — 用于西里尔字符的字符集转换 
        将字符由一种 Cyrillic（西里尔） 字符集转换成另一种Cyrillic（西里尔） 字符集， 
        利用西里尔字母伪造苹果官网域名：
        http://www.guancha.cn/TMT/2017_04_20_404498.shtml

----------------------------------

邮件相关：
------------------------------
convert_uuencode — 使用 uuencode 编码一个字符串
    uuencode是将二进制文件转换为文本文件的过程，转换后的文件可以通过纯文本e-mail进行传输， 在接收方对该文件进行uudecode，即将其转换为初始的二进制文件。
    转换后的文件中仅包含可打印字符
    uuencode 运算法则将连续的 3字节编码转换成 4字节（8-bit 到 6-bit）的可打印字符
convert_uudecode — 解码一个 uuencode 编码的字符串

quoted_printable_encode — 将 8-bit 字符串转换成 quoted-printable
    Quoted-printable可译为“可打印字符引用编码”，编码常用在电子邮件中，它是MIME编码常见一种表示方法
    Quoted-printable将任何8-bit字节值可编码为3个字符：一个等号"="后跟随两个十六进制数字(0–9或A–F)表示该字节的数值。
        例如，ASCII码换页符（十进制值为12）可以表示为"=0C"， 
              等号"="（十进制值为61）必须表示为"=3D"，gb2312下“中”表示为=D6=D0。
              除了可打印ASCII字符与换行符以外，所有字符必须表示为这种格式。
    Quoted-printable编码简单、方便因此在电子邮件中应用广泛
quoted_printable_decode — 将 quoted-printable 编码字符串转换为 8-bit 字符串

----------------------------------

quotemeta — 转义元字符集，包括：. \ + * ? [ ^ ] ( $ )
            ??这个是什么的元字符集??
            转义shell命令和参数应该使用：escapeshellcmd和escapeshellarg
            转义正则表达式，使用preg_quote
str_rot13 — 对字符串执行 ROT13 转换，即26个英文字母前13个转换为后13个，后13个转换为前13个


setlocale — 设置地区信息，进程级别，非线程级别
localeconv — Get numeric formatting information
            返回一个关联数组
            数组是当前地区的数字和货币的格式信息
            由setlocale设定
nl_langinfo — Query language and locale information
            根据参数返回当前地区的某一个信息






levenshtein — 计算两个字符串之间的编辑距离
similar_text — 计算两个字符串的相似度
soundex — Calculate the soundex key of a string
metaphone — Calculate the metaphone key of a string




多用途Internet邮件扩展
RFC 2045 - Multipurpose Internet Mail Extensions (MIME) 


  [1]: d:%5CwPS%E5%BF%AB%E7%9B%98%5CPHP%5CPHP%E6%89%8B%E5%86%8C%E7%AC%94%E8%AE%B0%5C04%E8%AF%AD%E8%A8%80%E5%8F%82%E8%80%83%5C%E5%BD%92%E7%BA%B3%E6%95%B4%E7%90%86%5Cprintf-format%E5%8F%82%E6%95%B0%E6%A0%BC%E5%BC%8F.png "aaa"
