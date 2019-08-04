---
title: Radiotray-相当小巧的linux广播软件
date: 2011-11-18T03:14:41+00:00
layout: post
categories:
  - Linux
tags:
  - radiotray
---

今天在ubuntu中文论坛看帖子时，发现了这个好东西。喜欢听广播的朋友，一定不能错过这款软件。其他发行版应该也能安装。增加了下面的播放列表后，可以在线收听台湾和日本电台。

首先，需要安装radiotray。
```
sudo apt-get install radiotray
```
之后，运行radiotray，使其停靠在indicater区域。默认地，安装之后，可以收听一下分类音乐广播。打开`~/.local/share/radiotray`,使用gedit编辑`bookmarks.xml`文件，将底下的文件内容插进到`<root>`的下一行。

[下载这个文件](http://115.com/file/bhk9fz5v#)

**频道来源及参考文献**

台湾频道：HiChannel http://hichannel.hinet.net/

日本频道：サイマルラジオ http://www.simulradio.jp/

参考文献：http://www.ubuntu-tw.org/modules/newbb/viewtopic.php?post_id=147598