---
layout: post
title:  "Unix命令之Du: 查看文件大小"
date:   2013-10-31 12:00:00
tags:	tech
myTag:	unix

---

# Du 命令

经常使用shell的时候做一些文件输入输出操作，今天将wiki离线数据包中的一些条目输出来。shell用得那么舒服，自然不会想要去GUI下看究竟输出后的文件有多大。那么万能的Unix命令一定能够帮助我。于是今天新学会的命令就是`du`，display disk usage statistics.

如果直接使用du命令，将会递归的显示当前目录和子目录下所有文件夹的大小。对，是文件夹而不是文件。如果我们需要看每一个文件的话可以添加`-a`参数。

	du -a

得到的结果可能是(这里用我自己一个文件夹做例子)

	16	./.DS_Store
	904	./A.record
	8	./idea.md
	1024008	./wiki_partaa
	1024000	./wiki_partab
	1024000	./wiki_partac
	1024000	./wiki_partad
	1024000	./wiki_partae
	1024000	./wiki_partaf
	1024000	./wiki_partag
	1024000	./wiki_partah
	263848	./wiki_partai
	8455848	./zhwiki-latest-pages-articles.xml
	16912632	.

每一行的前一项是文件大小，后一项是文件名称。但是这样子显示的文件大小只有一堆数字太不人性化了，怎么样才能把文件大小显示得更人性化呢？你可以选择添加参数`-k`，以1024bytes计算；`-m`，以MB计算；`-g`，以GB计算。但是单单使用其中一个参数有个缺点，那就是用这些单位计算时，它们并没有小数部分，而是采取进一制来显示的，也就是原本只有100MB的东西，采用-g后，它显示的是1而不是0或0.1。当然更好的方法就是添加参数`-h`，它会以K、M、G做单位显示，增加可读性。

	du -ah

得到结果

	8.0K	./.DS_Store
	452K	./A.record
	4.0K	./idea.md
	500M	./wiki_partaa
	500M	./wiki_partab
	500M	./wiki_partac
	500M	./wiki_partad
	500M	./wiki_partae
	500M	./wiki_partaf
	500M	./wiki_partag
	500M	./wiki_partah
	129M	./wiki_partai
	4.0G	./zhwiki-latest-pages-articles.xml
	8.1G	.

不过我有一个疑惑，像例子中`A.record`这个文件，在没有使用-h的情况下显示`904`，但是在使用-h的情况下显示`452K`，所以在没有使用-h的情况下，它文件大小的单位是什么呢？知道的朋友不妨留个言告诉一下我，谢谢!

--------------------------------------------------------------

### 参考资料

[brucexu1978的专栏, CSDN, linux du命令](http://blog.csdn.net/brucexu1978/article/details/7562285)
