---
title: 让Windows Media Player支持PLS格式
date: 2009-07-27T01:03:20+00:00
layout: post
categories:
  - 我的世界
tags:
  - windows
---

今天晚上想听一会[shoutcast](http://www.shoucast.com/)的音乐广播，于是打开网站，用网站自带的播放器，不知道怎么回事，一直循环着两首歌曲，很郁闷，总以为是自己的网络有问题，于是设置了用系统自带的播放器播放，可惜的是Windows Media Player不支持shoutcast的pls格式，该格式文件是Winamp播放器的专用播放列表文件，而我电脑里面却只安装了千千静听，这软件不支持PLS格式，很头疼，总不能再让我安装一个Winamp吧，于是google了一下。

嘿嘿，终于让我从[这里](http://weblogs.asp.net/jgalloway/archive/2004/12/21/328265.aspx)发现还真有这么一个类似插件的软件实现了Windows Media Player支持PLS的功能。需要的朋友可以从下面的地址去下载。

[下载该软件](http://openplsinwmp.codeplex.com/)

要求：.net framework 2.0

首先安装.net framework，然后安装openplsinwmp软件，之后关联PLS文件格式的打开方式为OpenPlsInWmp.exe。

打开资源管理器，点击菜单的“工具”，“文件夹选项”，点击“文件类型”，然后新建一个文件类型，扩展名PLS，点击“确定”完成新建文件类型的操作。

1.打开文件夹选项窗口

2.新建PLS文件格式

3.关联打开程序为OpenPlsInWmp.exe

4.打开Shoucast网站，选择一个广播，点击TuneIn，就可以收听节目了。嘿嘿。

PS：我经常收听SKY.FM的Jazz和NewAge广播节目，非常好听的。推荐一下。
