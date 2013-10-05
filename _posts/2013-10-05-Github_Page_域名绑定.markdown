---
layout: post
title:  "Github Page 域名绑定"
date:   2013-10-05 00:00:00
myTag:	web

---

Github Page 域名绑定
--------------------

我的这个网页是用Github+Jekyll来做的，然后昨天终于在godaddy买了这个域名了($9.99)，不过还是觉得挺值的~ 然后重点就来了，我想把`edlinlink.github.io`这个域名绑定到`edlin.me`这个新买的域名去。那么以后我只要输入`edlin.me`就能直接访问我的主页了。步骤如下：

1. 首先在自己项目的根目录中新建一个`CNAME`文件，内容为你要绑定的域名。例如我的CNAME文件内容为：

		edlin.me

2. 在godaddy管理自己的域名，为自己的域名添加一条A记录指向服务器所在的IP地址。我在网上搜索的时候就看不懂这句话，所以我会仔细讲讲步骤。

	2.1 首先登录godaddy进入自己的域名管理界面

	2.2 点击选择DNS->DNS Manager，等待页面跳转，然后点击你自己域名下面的`Edit Zone`

	2.3 因为我要绑定的是一个顶级域名，所以需要添加一条A记录(如果绑定的是一个二级域名好像是添加CNAME记录，不过这里就不谈这个了)。修改A(Host)栏，`@`后面的`Points to`的内容。将它改成`204.232.175.78`(我当时在想难道这个IP是每个人都一样的吗?答案是确定的，不过这个地址有可能会变，所以你添加的时候请参考[Github Help](https://help.github.com/articles/my-custom-domain-isn-t-working)，里面会讲到一个IP的。没错就是这个IP。)

	修改完之后应该如图所示：  

	![domain img](../picture/Github_Page_域名绑定-1.png)

	2.4 修改好Points to然后记得保存哦。等待一段时间，官方说最长48h会生效，但实际大概10min就能够生效了。

好了，至此一切大功告成。Enjoy your own domain!
