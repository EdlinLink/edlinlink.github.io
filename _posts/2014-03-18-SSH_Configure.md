---
layout: post
title:  "SSH Configure"
date:   2014-03-18 12:00:00
tags:	tech
myTag:	ssh

---

# SSH Configure

--------------------------

使用Mac总是会有一些和Lunix不一样的情况发生，虽然喜欢Mac的一些特点，但是偶尔需要解决一些在Lunix下很简单在Mac下变得超复杂的问题，还是十分使人烦躁。主要还是因为Mac的网络资源比较难找呀。

----------------------------------------------------------------------------

### SSH 远程登录

1\. 如果你正在使用电脑A，而想远程登录电脑B，可以通过设置SSH的方法。假设电脑B的用户名叫做ComputerB，ip地址为172.18.71.164。那么可以通过命令登录。然后通常会要求用户输入ComputerB的密码，输入后就能远程登录了。

	ssh ComputerB@172.18.71.164


2\. 如果你两台电脑的用户名都是一样的话，也可以直接通过ip登录。

	ssh 172.18.71.164


3\. 如果你的ip是相对固定的，你可以通过在/etc/hosts文件中写下对应的ip和名称，通过名称登录。例如在文件中添加`172.18.71.164   ubuntu`。
	
	ssh ubuntu

----------------------------------------------------------------------------

### SSH 免密码登录

当然，有的时候你希望两台都是自己的电脑，如果可以省去输入密码这一步就好了。这也是可以实现的。首先你需要进入自己用户目录下的.ssh文件夹(Mac:/Users/username; Lunix:/home/username; 如果没有则自己生成)。执行

	ssh-keygen -t rsa

于是在文件夹内生成了私钥`id_rsa`和公钥`id_rsa.pub`。将公钥复制到电脑B的.ssh文件夹内，并重命名为`authorized_keys`。系统会要求authorized_keys的权限不能设得太高，所以我们需要将它的权限设为600。

	chmod authorized_keys 600

现在我们再尝试用ssh登录。第一次登录会询问你是否添加到本地的known_hosts。选择yes，然后以后就不要需要密码也能够从电脑A远程登录电脑B了。

----------------------------------------------------------------------------

### 打开sshd服务

上面所说的远程登录的前提是，你要已经安装了SSH。其实细分下来SSH包含了client和server两个部分。client部分就是能够远程登录别人的机器，server当然是别人登录自己的机器。只有你打开了这两项服务，你才能够进行远程登录。如果你只选择打开client，那么也是可以的。

怎么查看自己的机器有没有打开这两项服务。client服务就是用户在命令行输入ssh时候启动的，所以这个很好知道。而server服务，最简单的方法就是测试能不能ssh到本机。

	ssh localhost

如果机器没有打开ssh-server服务，那么会返回

	ssh: connect to host localhost port 22: Connection refused

另一种查看的方法就是输入以下命令，看有没有出现sshd。

	ps -e | grep ssh

如果在苹果的电脑中需要执行sshd服务(就是server服务)可以通过以下命令:

	sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist

	sudo launchctl unload -w /System/Library/LaunchDaemons/ssh.plist

前者是启动sshd服务，后者是停止ssh服务。

----------------------------------------------------------------------------

### 参考资料

1. [ssh-keygen生成密钥设置方法. TTTiger88的ChinaUnix博客.](http://blog.chinaunix.net/uid-26838216-id-3191343.html)

2. [mac无法ssh localhost. 红黑联盟.](http://www.2cto.com/os/201203/123274.html)

