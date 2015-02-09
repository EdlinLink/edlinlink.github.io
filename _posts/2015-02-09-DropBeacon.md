---
layout: post
title:  "DropBeacon 介绍"
date:   2015-02-09 12:00:00
tags:	tech
myTag:	hardware

---

# DropBeacon 

---------------------------------------------------------------------------

在介绍DropBeacon前，我们需要知道什么是[iBeacon](http://baike.baidu.com/link?url=P6oWBiLGjNPX-7zbLopwlFGG6E4o2_WZ_XcqYoyUZVkQ8FSca4UTzH2T_h-Vnk2vwwhSY_rCcc2bNgrpGp6xJq)。简单来说，Beacon就是蓝牙，只不过是低功耗的蓝牙。iBeacon是苹果公司配备在iPhone上的一个功能，通过手机获知附近的蓝牙装置(Beacon)并进行一些操作(例如室内定位，定点推送等)。

本文介绍的Beacon是我在公司实习期间使用得比较好的Beacon，名字叫[DropBeacon](http://www.idropbeacon.com/)。


## DropBeacon 简介

DropBeacon和其他品牌的Beacon不同，是默认已经启动的了，所以都买回来的DropBeacon大多电量只有80%左右，而且它是无法拆卸，也就是无法自行换电池，这亮点使得它设计上更好看，但是也是它的软肋。


## DropBeacon 配置

配置DropBeacon需要在苹果设备上下载iBeaconKit App工具，拿一个DropBeacon靠近手机(放在手机上面)，远离其他Beacon，点击扫描。如果连接成功就会提示可以调整UUID，Major，Minor，功率，频率的信息。

UUID是可以随机生成的，没有办法生成特定的UUID，但是多个Beacon可以共用一个UUID，只要你在iBeaconKit中保存过这个UUID即可。我们可以把 < UUID, Major, Minor \> 看作是一个Beacon的真实ID。那为什么要分成UUID，Major，Minor呢？这是为了更方便的管理。像在我们的项目中，一个UUID代表一个商圈，Major代表楼层，Minor表示单独的Beacon。

需要注意的是每一个Beacon都有一个名字，DropBeacon的名字应该是以iDB开头的，但是这个名字不是随机的，例如一个UUID=453C2AAB-D044-4194-B3A6-C85AA1E6VA52, Major=3, Minor=76的Beacon，它的名字为: iDB450003004C\_。这个名字是由"iDB"加上UUID的前两位，加上Major的十六进制，加上Minor的十六进制，最后再加一个随机的字符组成的。



## iBeaconKit App 小结

1. iBeaconKit app扫描Beacon后，如果现场的Beacon太多(例如超过100个)，它可能会扫描到远远超过100个(例如五六百)，这个时候最好的方法是远离其他蓝牙和Beacon，尽量控制附近范围内只有少于100个Beacon。
2. 功率默认显示的是"0dBm"，0dBm对应的其实就是1mW(1微瓦)。"dBm"与"mW"的单位换算可以参考百度百科[dBm](http://baike.baidu.com/link?url=XvP1yibIonR311TKW-L75Y59uN2YZmUMJNffXSItuDSyv_a07bUJd5G2H4mkokLtZDQmPkGKGLBiM7sIzo1cK_)。
3. 频率表示Beacon 1秒内对外发出信号的次数。频率值越低，表示对外发出信号越多，这有利于手机接受信号，但是它的负面影响是耗电量加大。
4. DropBeacon预设的RSSI我相信是偏高的，所以测量出来的距离是偏短的，但是因为RSSI值预设偏高，所以比一般Beacon更容易搜索得到。



## Beacon 小结

1. 实际上Beacon不是特别适合做室内导航的，因为超过3m后，它的距离测量就不太精确了，但是如果有多个Beacon互相支持，那么它的精度能控制在1m左右。
2. Beacon最适合做的是场景定位，例如在前台付款时，手机和前台距离很近，这个时候可以很好的测量出距离，根据这个距离手机里面的app可以做一些操作(如推送信息)。
3. Beacon只是蓝牙，它只负责告诉手机，你和它当前的距离有多远，而不做其他事情。
4. 蓝牙的辐射是wifi的几十分之一，所以就算你有100个Beacon，也只与一个wifi辐射强度差不多。
5. Android手机扫描Beacon有一个扫描时间，这个扫描时间最好设定为2秒，因为这个扫描时间需要覆盖Beacon的信号发射频率，这样手机才可能同时搜索到多个Beacon进行定位或其他操作。例如Beacon的发射频率是1Hz，那么


## 参考资料

1. [iBeacon. baidu.com](http://baike.baidu.com/link?url=P6oWBiLGjNPX-7zbLopwlFGG6E4o2_WZ_XcqYoyUZVkQ8FSca4UTzH2T_h-Vnk2vwwhSY_rCcc2bNgrpGp6xJq)
2. [DropBeacon. idropbeacon.com](http://www.idropbeacon.com/)
3. [dBm. baidu.com](http://baike.baidu.com/link?url=XvP1yibIonR311TKW-L75Y59uN2YZmUMJNffXSItuDSyv_a07bUJd5G2H4mkokLtZDQmPkGKGLBiM7sIzo1cK_)
