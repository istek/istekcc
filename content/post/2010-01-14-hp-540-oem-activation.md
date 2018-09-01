---
title: HP 540笔记本通过OEM方式激活WIN7
date: 2010-01-14T21:58:26+00:00
layout: post
categories:
  - 我的世界
tags:
  - windows
---

我的笔记本式HP 540，前些日子使用软激活方式，无法激活，最后使用了神key激活了。我的本子无法升级BIOS使之支持SLIC 2.1，于是我想既然本子是惠普的，一定有一种OEM的方式能激活，于是搜索之，发现了方法，特共享出来。

从[我的网盘](http://cid-574f8e37bafc4791.skydrive.live.com/browse.aspx/.Public/vista)下载HP.ZIP文件，解压文件，内有HP.EXE文件，该文件是惠普提供的一个工具软件，主要是用来修改BIOS中的一些信息，比如SKU号，序列号，model等等。将该文件复制到U盘，使用U盘引导系统到DOS模式，或者，使用一些ghost光盘也可以，只要能进入DOS模式，就可以。

然后locate到hp.exe文件所在的位置，输入以下命令：
```
hp write slp on
```
<!--more-->
该命令的含义是启用BIOS中的SLIP功能。更多命令，请直接输入hp.exe回车。

之后使用小马哥的OEM激活工具，就可以完美激活了。该方法适用不适用其他的惠普笔记本，我不敢妄下断论，请酌情使用。

再者，目前各大论坛的WIN7版本都是7600版的，大多都是16385这个RTM零售版，其实与MSDN上下载的版本是一样的，没有必要为了MSDN四个字重新再下载一次。

PS: 感谢 CG落伍者 纠正了命令参数。