---
title: MySQL - Manual - 10.10 - 字符集：支持的字符集和校对规则 - Supported Character Sets and Collations
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 字符集：支持的字符集和校对规则

<!--more-->

这一节指出 MySQL 支持哪些儿字符集。每组相关字符集都有一个子部分。对于每个字符集
，都列出了允许的校对规则。

要列出可用的字符集及其默认校对规则，请使用 `SHOW CHARACTER SET` 语句或查询
`INFORMATION_SCHEMA CHARACTER_SETS` 表。例如:
```
mysql> SHOW CHARACTER SET;
+----------+---------------------------------+---------------------+--------+
| Charset | Description | Default collation | Maxlen |
+----------+---------------------------------+---------------------+--------+
| armscii8 | ARMSCII-8 Armenian | armscii8_general_ci | 1 |
| ascii | US ASCII | ascii_general_ci | 1 |
| big5 | Big5 Traditional Chinese | big5_chinese_ci | 2 |
| binary | Binary pseudo charset | binary | 1 |
| cp1250 | Windows Central European | cp1250_general_ci | 1 |
| cp1251 | Windows Cyrillic | cp1251_general_ci | 1 |
| cp1256 | Windows Arabic | cp1256_general_ci | 1 |
| cp1257 | Windows Baltic | cp1257_general_ci | 1 |
| cp850 | DOS West European | cp850_general_ci | 1 |
| cp852 | DOS Central European | cp852_general_ci | 1 |
| cp866 | DOS Russian | cp866_general_ci | 1 |
| cp932 | SJIS for Windows Japanese | cp932_japanese_ci | 2 |
| dec8 | DEC West European | dec8_swedish_ci | 1 |
| eucjpms | UJIS for Windows Japanese | eucjpms_japanese_ci | 3 |
| euckr | EUC-KR Korean | euckr_korean_ci | 2 |
| gb18030 | China National Standard GB18030 | gb18030_chinese_ci | 4 |
| gb2312 | GB2312 Simplified Chinese | gb2312_chinese_ci | 2 |
| gbk | GBK Simplified Chinese | gbk_chinese_ci | 2 |
| geostd8 | GEOSTD8 Georgian | geostd8_general_ci | 1 |
| greek | ISO 8859-7 Greek | greek_general_ci | 1 |
| hebrew | ISO 8859-8 Hebrew | hebrew_general_ci | 1 |
| hp8 | HP West European | hp8_english_ci | 1 |
| keybcs2 | DOS Kamenicky Czech-Slovak | keybcs2_general_ci | 1 |
| koi8r | KOI8-R Relcom Russian | koi8r_general_ci | 1 |
| koi8u | KOI8-U Ukrainian | koi8u_general_ci | 1 |
| latin1 | cp1252 West European | latin1_swedish_ci | 1 |
| latin2 | ISO 8859-2 Central European | latin2_general_ci | 1 |
| latin5 | ISO 8859-9 Turkish | latin5_turkish_ci | 1 |
| latin7 | ISO 8859-13 Baltic | latin7_general_ci | 1 |
| macce | Mac Central European | macce_general_ci | 1 |
| macroman | Mac West European | macroman_general_ci | 1 |
| sjis | Shift-JIS Japanese | sjis_japanese_ci | 2 |
| swe7 | 7bit Swedish | swe7_swedish_ci | 1 |
| tis620 | TIS620 Thai | tis620_thai_ci | 1 |
| ucs2 | UCS-2 Unicode | ucs2_general_ci | 2 |
| ujis | EUC-JP Japanese | ujis_japanese_ci | 3 |
| utf16 | UTF-16 Unicode | utf16_general_ci | 4 |
| utf16le | UTF-16LE Unicode | utf16le_general_ci | 4 |
| utf32 | UTF-32 Unicode | utf32_general_ci | 4 |
| utf8 | UTF-8 Unicode | utf8_general_ci | 3 |
| utf8mb4 | UTF-8 Unicode | utf8mb4_0900_ai_ci | 4 |
+----------+---------------------------------+---------------------+--------+
```

在一个字符集有多个校对规则的情况下，可能不清楚哪个校对规则最适合给定的应用程序。
为了避免选择错误的校对规则，对具有代表性的数据值进行一些比较是很有帮助的，以确保
给定的校对规则能够按照预期的方式对值排序。

## 10.10.1 Unicode Character Sets

MySQL 支持多个 Unicode 字符集：
* utf8mb4: Unicode 字符集的 UTF-8 编码，每个字符使用 1 到 4 个字节
* utf8mb3: Unicode 字符集的 UTF-8 编码，每个字符使用 1 到 3 个字节
* utf8: utf8mb3 的别名
* ucs2: Unicode 字符集的 UCS-2 编码，每个字符使用 2 个字节
* utf16: Unicode 字符集的 UTF-16 编码，每个字符使用 2 或 4 个字节，类似 ucs2，但
  有对补充字符的扩展。
* utf16le: Unicode 字符集的 UTF-16LE 编码，像 utf16 但是小端（little-endian）而
  不是大端（big-endian）
* utf32: Unicode 字符集的 UTF-32 编码，每个字符使用 4 个字节。

utf8 和 ucs2 仅支持基本多语平面（Basic Multilingual Plane，BMP）字符，utf8mb4,
utf16, utf16le, 和 utf32 支持 BMP 和补充字符。

本节描述 Unicode 字符集可用的校对规则及其区别属性。关于 Unicode 的一般信息，参考
：Section 10.9, “Unicode Support”.

大多数 Unicode 字符集都有：
* 一个通用的校对规则（用名字里包含 `_general` 表示，或者名字里没有语言说明符也表示通用规则）
* 一个二进制校对规则（用名字中包含 `_bin` 表示）
* 一些特定于语言的校对规则（用语言说明符表示）。
例如，对于 `utf8` 字符集，`utf8_general_ci` 和 `utf8_bin` 分别是它的通用校对规则
和二进制校对规则，而 `utf8_danish_ci` 是它特定语言的校对规则之一。

`utf16le` 的校对规则支持是有限的，可用的校对规则只有 `utf16le_general_ci` 和
`utf16le_bin`，分别类似于 `utf16_general_ci` 和 `utf16_bin`.

下表中显示的地区代码或语言名称表示特定于语言的校对规则。Unicode 字符集可以包括一
种或多种语言的校对规则。

Unicode 校对规则语言说明符：

    Language                            Language Specifier
    Classical Latin 古典拉丁                la or roman
    Croatian 克罗地亚                       hr or croatian
    Czech 捷克                              cs or czech
    Danish 丹麦                             da or danish
    Esperanto 世界语                        eo or esperanto
    Estonian 爱沙尼亚                       et or estonian
    German phone book order 德国电话本订单  de_pb or german2
    Hungarian 匈牙利                        hu or hungarian
    Icelandic 冰岛                          is or icelandic
    Japanese 日本                           ja
    Latvian 拉脱维亚                        or latvian
    Lithuanian 立陶宛                       lt or lithuanian
    Persian 波斯                            rsian
    Polish 波兰                             pl or polish
    Romanian 罗马尼亚                       ro or romanian
    Russian 俄罗斯                          ru
    Sinhala 僧伽罗                          sinhala
    Slovak 斯洛伐克                         sk or slovak
    Slovenian 斯洛维尼亚                    sl or slovenian
    Modern Spanish 现代西班牙               es or spanish
    Traditional Spanish 传统西班牙语        es_trad or spanish2
    Swedish 瑞典                            sv or swedish
    Turkish 土耳其                          tr or turkish
    Vietnamese 越南                         vi or vietnamese

克罗地亚语的校对规则是为这些克罗地亚字母量身定做的：`Č, Ć, Dž, Đ, Lj, Nj, Š, Ž`

丹麦的校对规则也可用于挪威

对于日语， `utf8mb4` 字符集包括 `utf8mb4_ja_0900_as_cs` 和
`utf8mb4_ja_0900_as_cs_ks` 校对规则。这两种校对规则都是口音敏感和区分大小写的。
`utf8mb4_ja_0900_as_cs_ks` 还是假名敏感的，它将片假名字符与平假名字符区分开来，
而 `utf8mb4_ja_0900_as_cs` 则将片假名字符与平假名字符排序时视为平等。需要日本校
对规则而不需要假名敏感性的应用程序可以使用 `utf8mb4_ja_0900_as_cs` 来获得更好的
排序性能。 `utf8mb4_ja_0900_as_cs` 使用三个重量级别（three weight levels）进行排
序;`utf8mb4_ja_0900_as_cs_ks` 使用四个。

对于经典的拉丁校对规则，它们是重音不敏感的，I和J比较时相等，U和V比较时相等。I和J
，U和V在基本字母水平上(on the base letter level)是相等的。换句话说，J被认为是重
音I，U被认为是重音V。

西班牙语校对规则适用于现代西班牙语和传统西班牙语。对于这两种情况，`ñ`(n 上面加波
浪字符)是在n和o之间的一个单独的字母。另外，对于传统的西班牙语，`ch`是c和d之间的
一个单独的字母，ll是l和m之间的一个单独的字母。

传统的西班牙语校对规则也可用于阿斯图里亚和加利西亚。

瑞典校对规则包括瑞典语的规则。例如，在瑞典语中，以下的关系保持不变，这不是德国人
或法国人所期望的：
```
Ü = Y < Ö
```

For questions about particular language orderings, unicode.org provides Common Locale Data Repository (CLDR) collation charts at http://www.unicode.org/cldr/charts/30/collation/index.html.

The xxx_general_mysql500_ci collations preserve the pre-5.1.24 ordering of the original xxx_general_ci collations and permit upgrades for tables created before MySQL 5.1.24 (Bug #27877).

MySQL implements the xxx_unicode_ci collations according to the Unicode Collation Algorithm (UCA) described at http://www.unicode.org/reports/tr10/. The collation uses the version-4.0.0 UCA weight keys: http://www.unicode.org/Public/UCA/4.0.0/allkeys-4.0.0.txt. The xxx_unicode_ci collations have only partial support for the Unicode Collation Algorithm. Some characters are not supported, and combining marks are not fully supported. This affects primarily Vietnamese, Yoruba, and some smaller languages such as Navajo. A combined character is considered different from the same character written with a single unicode character in string comparisons, and the two characters are considered to have a different length (for example, as returned by the CHAR_LENGTH() function or in result set metadata).

Unicode collations based on UCA versions later than 4.0.0 include the version in the collation name.  Thus, utf8mb4_unicode_520_ci is based on UCA 5.2.0 weight keys (http://www.unicode.org/Public/ UCA/5.2.0/allkeys.txt), whereas utf8mb4_0900_ai_ci is based on UCA 9.0.0 weight keys (http:// www.unicode.org/Public/UCA/9.0.0/allkeys.txt).

Collations based on UCA 9.0.0 and higher are faster than collations based on UCA versions below 9.0.0.  They also have a pad attribute of NO PAD, in contrast to PAD SPACE as used in collations based on UCA versions below 9.0.0. NO PAD collations treat spaces at the end of strings like any other character.  To determine the pad attribute for a collation, use the INFORMATION_SCHEMA COLLATIONS table, which has a PAD_ATTRIBUTE column.

Comparisons of VARCHAR columns that have a NO PAD collation differ from other collations with respect to trailing spaces. For example, 'a' and 'a ' compare as different strings, not the same string.  MySQL implements language-specific Unicode collations if the ordering based only on UCA does not work well for a language. Language-specific collations are UCA-based, with additional language tailoring rules.
关于尾随空格，使用具有 `NO PAD` 属性的校对规则对 VARCHAR 列进行比较时，不同于其他校对规则。例如，`a` 和 `a ` 比较时为不同的字符串，而不是相同的字符串。MySQL实现特定于语言的 Unicode 校对规则，如果仅基于UCA的排序不能很好地适用于一种语言。特定于语言的校对规则是基于 UCA 的，且有额外的语言裁剪规则。

For example, the nonlanguage-specific `utf8mb4_0900_ai_ci` and language-specific `utf8mb4_LOCALE_0900_ai_ci` Unicode collations each have these characteristics:

• The collation is based on Unicode Collation Algorithm (UCA) 9.0.0 and Common Locale Data Repository (CLDR) v30, is accent insensitive, and case insensitive. These characteristics are indicated by `_0900`, `_ai`, and `_ci` in the collation name. Exception: `utf8mb4_la_0900_ai_ci` is not based on CLDR because Classical Latin is not defined in CLDR.

• The collation works for all characters in the range [U+0, U+10FFFF].

• If the collation is not language specific, it sorts all characters, including supplementary characters, in default order (described following). If the collation is language specific, it sorts characters of the language correctly according to language-specific rules, and characters not in the language in default order.

• By default, the collation sorts characters having a code point listed in the DUCET table (Default Unicode Collation Element Table) according to the weight value assigned in the table. The collation sorts characters not having a code point listed in the DUCET table using their implicit weight value, which is constructed according to the UCA.

• For non-language-specific collations, characters in contraction sequences are treated as separate characters. For language-specific collations, contractions might change character sorting order.

LOWER() and UPPER() perform case folding according to the collation of their argument. A character that has uppercase and lowercase versions only in a Unicode version more recent than 4.0.0 is converted by these functions only if the argument has a collation that uses a recent enough UCA version.

For any Unicode character set, operations performed using the `xxx_general_ci` collation are faster than those for the `xxx_unicode_ci` collation. For example, comparisons for the `utf8_general_ci` collation are faster, but slightly less correct, than comparisons for `utf8_unicode_ci`. The reason for this is that `utf8_unicode_ci` supports mappings such as expansions; that is, when one character compares as equal to combinations of other characters. For example, in German and some other languages `ß` is equal to ss. `utf8_unicode_ci` also supports contractions and ignorable characters. `utf8_general_ci` is a legacy collation that does not support expansions, contractions, or ignorable characters. It can make only one-to-one comparisons between characters.

To further illustrate, the following equalities hold in both `utf8_general_ci` and `utf8_unicode_ci` (for the effect of this in comparisons or searches, see Section 10.8.6, “Examples of the Effect of Collation”):



## 10.10.2 West European Character Sets




## 10.10.3 Central European Character Sets




## 10.10.4 South European and Middle East Character Sets




## 10.10.5 Baltic Character Sets




## 10.10.6 Cyrillic Character Sets




## 10.10.7 Asian Character Sets




## 10.10.8 The Binary Character Set




