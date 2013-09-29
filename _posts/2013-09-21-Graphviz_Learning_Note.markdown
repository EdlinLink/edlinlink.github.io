---
layout: post
title:  "Graphviz Learning Note"
date:   2013-09-21 00:00:00
categories: software

---


Graphviz 学习笔记
-------------------

Intorduction
-------------------
graphviz是一款简洁的绘图工具。一开始知道它是因为想要用它来画UML图，不过当时并没有学会，直到刚刚才仔细看了一下，发现还是很方便的。

Install & Run
-------------------
我是用homebrew在Mac下安装的。
直接打命令：

	brew install graphviz

安装好后，需要编写自己的dot文件。
dot文件是用dot语言编写的文件，简单明了，编写方法可以参考[Graphviz中文教程指南](http://wenku.baidu.com/view/f43d8fed172ded630a1cb605.html).

最简单的例子可以是：

	digraph G{ 
		main->parse->execute;
		main->init;
		main->cleanup;
		execute->make_string;
		execute->printf;
		init->make_string;
		main->printf;
		execute->compare;
	} 

我们使用最基本的方法编译test.doj生成test.jpg：

	dot -Tjpg test.dot -o test.jpg

![test.jpg](./picture/Graphviz_Learning_Note-1.jpg)

当然根据不同的dot文件可以画很多不同的图，例如：

![hash.jpg](./picture/Graphviz_Learning_Note-2.jpg)
![binarytree.jpg](./picture/Graphviz_Learning_Note-3.jpg)

graphviz支持最终保存成很多种格式，我们可以通过调整参数`-Tjpg`来实现最终的不同格式：

	dot -Tjpg test.dot -o test.jpg
	dot -Tps test.dot -o test.ps

当然graphviz有几种不同的layout的mode可供选择，不同的mode会导致最终的图不一样，以下是可供选择的mode：

+ dot
+ neato
+ twopi
+ circo
+ fdp
+ sfdp

不同的mode选择也就是把编译运行的命令中的`dot`换成对应其他的mode即可，这里就不插入图片了。

最后放上一张用graphviz画的facebook中100人的关系图：

![fb.jpg](./picture/Graphviz_Learning_Note-4.jpg)

