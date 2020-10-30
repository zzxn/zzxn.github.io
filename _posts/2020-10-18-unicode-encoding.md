---
title: Unicode字符编码概要
tags: 杂项 字符编码 Unicode
key: abb303716541c8b6c054d634fb96c320
---

## Unicode字符编码模型

首先讲「字符编码模型」，主要是为了阐明概念和说明术语，以提供讨论的上下文。

在Unicode Technical Report #17 [UNICODE CHARACTER ENCODING MODEL](http://www.unicode.org/reports/tr17/) 中，将字符编码的结构建模为下面4层：

* ACR: Abstract Character Repertoire 抽象字符表

    * The set of characters to be encoded, for example, some alphabet or symbol set

    * **抽象字符表定义了待编码的无序字符集合，抽象字符指的是字母或者符号的本体**

    * 很多ACR是封闭的，一经确定不能更改，但是Unicode标准中的ACR是开放的，可以不断扩充

        > Unicode的实现可以只支持Universal Repertoire的子集

* CCS: Coded Character Set 编号字符集

    * A mapping from an abstract character repertoire to a set of nonnegative integers
    * **编号字符集把抽象字符映射为（可以不连续的）非负整数，这个整数被称为抽象字符的码点（Code Point）**
    * 编号的范围被称为**编号空间（Code Space）**
    * 编号的作用是为了方便**管理和引用字符**
    * 例子：U+2013表示Unicode的 $2013_{(16)}$ 号字符

* CEF: Character Encoding Form 字符编码形式

    * A mapping from a set of nonnegative  integers that are element of a CCS to a set of sequences of particular code units of some specific width, such as 8-bit integers
    * **字符编码形式将每个字符编号映射为码元（Code Unit）序列，码元指的是固定位宽的整数**
    * 如果字符的码元个数都是一样的，则这种CEF被称为**定长编码**，否则被称为**变长编码**
    * 字符编码形式产生的码元序列是**逻辑上的编码**

* CES: Character Encoding Scheme 字符编码模式

    * A reversible transformation from a set of  sequences of code units (from one or more CEFs) to a serialized of bytes
    * **字符编码模式是码元序列到字节序列的可逆映射**
    * 字符编码模式涉及到字节序的问题（码元中字节的顺序），分为大端（Big-Endian）和小端（Little Endian）
    * 字符编码模式产生的字节流是**实际的物理编码**，也是程序员大部分时间实际打交道的编码

传统的字符编码模型是很简单的，一般会把一些字符进行编号，直接将其编号（的二进制编码）表示作为该字符的编码（例如ASCII）。

而像Unicode这样的现代字符编码模型则分了很多层，通过分层实现了字符集（ACR）、字符编号（CCS）、字符逻辑编码（CEF）和字符物理编码（CES）的解耦。

## ASCII

ASCII是最基础最重要的编码，很多编码都直接或间接地兼容它，它编码了英文环境中最常用的字母和标点符号，以及一些特殊的控制字符。

ASCII编码的编号（0-127）转换成byte就是其码元，也是其实际的物理编码，例如65号字符`A`的用byte表示是`0x41`，实际的物理编码也就是`0x41`。

## Unicode

> 本节主要参考 [Unicode标准13.0](https://www.unicode.org/versions/Unicode13.0.0/UnicodeStandard-13.0.pdf)

Unicode标准定义了一套有能力编码人类所有书写字符的编码系统，现在已被广泛使用，其中最流行的是UTF-8和UTF-16。

> 当我们说 [Unicode](https://home.unicode.org/) 时，我们有可能在说这些概念之一：
>
> * Unicode字符表
> * Unicode编号字符集
> * Unicode字符编码形式（UTF-8，UTF-16，UTF-32 encoding form，逻辑上的编码，不涉及字节序问题）
> * Unicode字符编码模式（UTF-8，UTF-16，UTF-32 encoding scheme，比起同名的编码形式，涉及到了字节序的问题）
>
> 注意：说起UTF-X时，我们有可能是在说其编码形式（encoding form），也有可能在说其编码模式（encoding scheme），后者需要BOM（Byte Order Mark）来标记字节序。
>
> 但是对于UTF-8来讲，其编码形式和编码模式是完全相同的，因为不涉及字节序问题，也不需要BOM，但是Windows记事本有时会自作聪明地加上BOM，导致其他软件读取时出现问题，这也是Python打开Windows编辑的UTF-8文本时出错的常见原因。
>
> 对于我们来说，应当坚持一致使用UTF-8 without BOM。

对于每个字符，Unicode标准都给它一个编号（code point）和一个名字。Unicode标准包含1,114,112（ $0 \to 10FFFF_{(16)}$ ）个码点，其中大多数都能用于编码字符。这样强大的表示能力足以让Unicode编码涵盖绝大部分现行的和历史上使用过的字符。

世界上的大部分常用字符（包括常用汉字）都被编码到最开始的65536（ $2^{16}$ , $0 \to 00FFFF_{(16)}$ ）个码点中，这些码点也被称为**基本多语言平面**（Basic Multilingual Plane，**BMP**）（共17个平面，每个平面都有65536个码点，除BMP的其他平面被称为“**增补平面**”，有兴趣可搜索了解）。

Unicode字符目前有且仅有三种编码形式（encoding form，即逻辑编码），32位的UTF-32，16位的UTF-16和8位的UTF-8。

* UTF-8是面向单个字节的，与ASCII编码兼容，在英文环境下很节省空间，因此被广泛使用。

* UTF-16在效率和空间之间有较好平衡，也是最早出现的编码，被Windows和Java等环境用作内部编码。

* UTF-32是最简单和最容易处理的编码，被Python用作字符串的内部编码。

### UTF-32

UTF-32是最简单的编码形式，每个Unicode码点被一个32位的码元直接表示。

它是一种定长编码，固定为4 bytes（32 bits），码元中的字节排列存在大小端问题，因此需要在字节流开头有BOM。

UTF-32编码对空间有很大浪费，因此不太常用。

Python3字符串就是Unicode Code Point序列（UTF-32）。

例子：U+10000的UTF-32编码形式为`00 01 00 00`。

### UTF-16

UTF-16是最早提出的、最复杂的编码形式，每个Unicode码点被1个或者2个16位的码元表示。

UTF-16是一种变长编码。

对于在基本多语言平面的字符，UTF-16编码形式使用单个16位码元来表示，如果只包含BMP字符，则UTF-16可被是为一种高效的定长编码。

对于在增补平面的字符，UTF-16编码形式使用一对16位的码元来表示，这一对码元被称为代理对（**surrogate pairs**）。用于代理对的码元集合和用于单个字符的码元集合是不重叠的，以便处理简单。

UTF-16单个码元中的两个byte如何排列涉及到字节序问题。

> UTF-16是最早提出的Unicode编码，那时Unicode还不完善，但还是试图提出一种16位的定长编码用于表示世界上所有字符，最开始这个看起来很不错，Windows开发团队和Java开发团队就把UTF-16作为了字符的内部编码方式——但是很快大家发现16位不够用了，各种奇奇怪怪的象形字符（各国汉字或者类汉字字符、emoji等）实在是太多了，因此就引入了复杂的代理对（surrogate pairs）来表示更多字符。

> 在Java 9及更高版本中，所谓的“紧凑字符串”功能是默认启用的，也就是对于 `ISO-8859-1 / Latin-1` 字符集，使用单字节进行编码，以便节省空间。

### UTF-8

这可能是互联网中最流行的Unicode编码格式，使用1-4个byte编码字符（理论上最多可以到6个byte）。

UTF-8是变长编码，使用8位的byte作为码元，每个码元的起始部分表明该码元byte所属的码元序列。

它与ASCII兼容，并且对于英文文本来说很省空间（对汉语等其他语言来讲，浪费空间）。对于英文文本，每个字符只需1 byte，但是对于汉字，每个字符需要3 byte，相对于GBK、UTF-16等编码来说，是较为浪费空间的。可以认为UTF-8对英文字符有针对性的压缩。

UTF-8是面向byte的，与ASCII兼容，并且由于它的码元是单字节的，所有**没有字节顺序问题**。

UTF-8是有很好的鲁棒性，因为它没有状态，而且错误不会传递。

#### UTF-8编码规则

对于UTF-8文本流中的字节（码元），如果

* 字节首位为0，表示它是一个单字节字符
* 字节首位为110，表示它是一个双字节字符的起始字节
* 字节首位为1110，表示它是一个三字节字符的起始字节
* 字节首位为11110，表示它是一个四字节字符的起始字节
* 字节首位为10，表示它是一个多字节字符的中间字节

每个字节中的其余位都按照码点值的位顺序逐字节填充，如下表所示：

| Scalar Value                 | First Byte | Second Byte | Third Byte | Fourth Byte |
| ---------------------------- | ---------- | ----------- | ---------- | ----------- |
| `00000000 0xxxxxxx`          | `0xxxxxxx` |             |            |             |
| `00000yyy yyxxxxxx`          | `110yyyyy` | `10xxxxxx`  |            |             |
| `zzzzyyyy yyxxxxxx`          | `1110zzzz` | `10yyyyyy`  | `10xxxxxx` |             |
| `000uuuuu zzzzyyyy yyxxxxxx` | `11110uuu` | `10uuzzzz`  | `10yyyyyy` | `10xxxxxx`  |


-------------------------------------

> 参考资料：[刨根究底字符编码](https://www.cnblogs.com/benbenalin/p/6881980.html)。
