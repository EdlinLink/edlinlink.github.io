---
layout: post
title:  "Grep 的简单实现"
date:   2013-10-27 12:00:00
tags:	tech
myTag:	grep

---

# Grep命令实现

究竟代码怎么样才能写得好，或者正如《代码之美》这本书的名字，怎么样才能写得“美”呢？看看这本书或许能有收获。这本书第一章就讲了一个Grep命令的实现。

`Grep`是一个我们常常能看到命令(在此之前，其实我是不知道这个命令是做什么的...)它其实是一个查找子串的命令。它的全称是`Global Regular Expression Print`，表示一个正则表达式能不能在某个文件中到得到。

-------------------------------

***这里要弄清楚`wildcard`和`regular expression`的区别哦! 像在wildcard中`*`表示的是匹配0个或多个任意字符，而在regular expression中则表示匹配任意多个(0个或以上)星号前一个字符。如果不清楚的话，可以参考[Sell十三问](http://bbs.chinaunix.net/thread-218853-1-1.html)这篇文章中最后的补充问题。***

-------------------------------

grep中代码的长度超过500行，开源的正则表达式软件包则更庞大。但是贝尔实验室的`Rob Pike`却只用了大约30行的C代码写成了一个最小的正则表达式实例。而且代码确实写得很好，清晰明了。

先来定义一下一些字符的含义：

+ `.`:	匹配任意的单个字符  
+ `^`:	匹配输入字符串的开头  
+ `$`:	匹配输入字符串的结尾  
+ `*`:	匹配前一个字符的零个或多个出现  


下面就是grep的代码：

	int match(char *regexp, char *text){
		if(regexp[0]=='^')
			return matchhere(regexp+1, text);

		do{
			if(matchhere(regexp, text))
				return 1;
		}while(*text++!='\0');

		return 0;
	}

	int matchhere(char *regexp, char *text){
		if(regexp[0]=='\0')
			return 1;
		if(regexp[1]=='*')
			return matchstar(regexp[0], regexp+2, text);

		if(regexp[0]=='$' && regexp[1]=='\0')
			return *text == '\0';
		if(*text!='\0' && (regexp[0]=='.' || regexp[0]==*text))
			return matchhere(regexp+1, text+1);

		return 0;
	}

	int matchstar(int c, char *regexp, char *text){
		do{
			if(matchhere(regexp, text))
				return 1;
		}while(*text!='\0' && (*text++==c || c=='.'));

		return 0;
	}

请注意哦,星号在正则表达是的含义与在wildcard中不同，它在这里仅仅表示前一个字母的任意次出现而并不是任意多个任意字符。所以对于在`abcdefg`中寻找`*abcdefg`是返回找不到，但是如果寻找的是`x*abcdefg`则能够找到。
