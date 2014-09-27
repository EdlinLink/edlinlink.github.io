---
layout: post
title:  "URL Shortening"
date:   2014-09-26 12:00:00
tags:	tech
myTag:	interview

---

# URL Shortening

-----------------------------

## Introduction

"URL Shortening"中文翻译可以叫做"短网址"，或许这个名字听起来陌生，但是实际上你经常看到。微博的限定字数是140个字，有的时候我们需要添加一个链接进去，这个链接有可能很长，例如:

	http://zhidao.baidu.com/question/488691941257880332.html

这个时候，我们需要做一种URL的映射，把这个长的链接变成短的链接，以节省字数，例如:

	http://tinyurl.com/mtfgubv

其中"tinyurl.com"为服务器地址。这个就是我们今天的主题"URL Shortening"。

## Algorithm

根据我在网上查的资料，实现URL Shortening的算法主要有两种

### 1. random number generated

我们暂且将映射后的URL称为tinyURL。tinyURL能够使用的字符为[0-9][a-z][A-Z]一共62个字符，如果你不想最后生成的tinyURL看起来可能有意义，你可以选择去掉"a","e","i","o","u"，如果你想让tinyURL的字符不易混淆，可以选择去掉"O"和"0","1"和"l"等。此处我们不去掉任何的字符。

数据库中有一个自增的id，每当需要插入一个URL，id就增加1。给定一个URL，将当前id分配给这个URL，当前id是一个10进制的数字，我们现在定义一个62进制，而这个62进制就使用[0-9a-zA-Z]这62个字符作为标记。例如当前id为"1127"，则它对应的62进制字符为"jb"。那么原本的URL现在可以映射成"http://domain.com/jb"。

当你需要查找给定短网址是"jb"时的原URL，只需要将"jb"从62进制，转变成10进制"1127"，就可以马上从数据库上拿出原来URL这条记录。

这个方法的好处就是查找<id,URL,tinyURL>的方法很方便，每个tinyURL都是独一无二的；但是缺点就是如果某个URL已经产生过tinyURL了，那么如果该算法不先检查数据库中是否已经存在了对应的tinyURL，则同一个URL会存在两个tinyURL，所以实际使用中，我们在判断是否产生tinyURL前是需要检查数据库中究竟有没有对应的记录。

### 2. hash function

给定一个URL，我们希望能够映射成一个独一无二的短的tinyURL。对于一个URL，如果我们对它进行md5操作，那么它将产生一个32个字节的code。这个code不能说一定不会重复，但是至少我们能说这个md5 code很少有概率能够重复。如果我们将这32个bytes分成4份，每份是8个bytes(32 bits)。现在我们只考虑这32个bits的后面30个bits，每5个bits为一组，一共有6组。将每组的5个bits作为索引，映射成[0-9a-zA-Z]中的字符，于是获得了一个6个字符的编码。最终一共有4组这样的编码，即一个URL能够生成4组tinyURL。

使用这种方法的优点是相同的URL进行变换时，能够获得相同的tinyURL；但是缺点就是，让你知道一个tinyURL时，需要进入数据库中查找对应的URL究竟时什么。如果这么不凑巧，有两个URL对应同一个tinyURL，可能要对第二个URL对应的tinyURL做一些调整，就像处理hash collision的情况，例如将该tinyURL+1或者增加一位。


## Reference

1. [URL shortening. Wikipedia](http://en.wikipedia.org/wiki/URL_shortening)
2. [How to code a URL shortener? stackoverflow](http://stackoverflow.com/questions/742013/how-to-code-a-url-shortener)
3. [短地址. 百度百科](http://baike.baidu.com/view/5712914.htm?fr=aladdin)
4. [微博短网址原理的算法. 开源中国社区](http://www.oschina.net/question/59519_82623)

