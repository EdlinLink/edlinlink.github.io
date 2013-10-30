---
layout: post
title:  "Unix命令 - split: 文件分割命令"
date:   2013-10-31 12:00:00
tags:	tech
myTag:	unix

---

# Split 文件分割命令

今天下载好了维基百科中文版的数据包，800+M解压后有4.33G这么大。想要看一下文件是以什么样的格式存储的。用vim和sublime打开都无果，才想起自己的电脑才4G内存，内存吃不消呀。尝试用C++去读这个文件，不知道什么原因也读读不出来。上网搜索了一番，有人说可以用split命令去将大文件进行分割，果然可行。下面是关于split的一点点介绍。

## 分割

split命令有多种模式可以选择，详细可以`man split`来看看。但是常用的应该是以下两种。

	split -l 300 large_file new_file_prefix

上面这个命令将`large_file`按每300行分割开来，存储在以`new_file_prefix`开头的n个文件中。

	split -b 10m large_file new_file_prefix

这是另外一中模式，将`large_file`按照每10m大小进行划分，同样存储在以`new_file_prefix`开头的n个文件中。

## 合并

如果需要将这些分割开来的文件合并到一个文件中去，需要用到`cat`命令。

	cat small_files* > large_file


有了`split`命令，以后大文件的分割就不用再那么麻烦了! 突然觉得Unix真的好伟大，看来是要找时间好好看看Unix的入门书籍才行~


## 参考资料

[51CTO.com, Linux文件分割与合并:split&cat.](http://os.51cto.com/art/201104/255359.htm)
