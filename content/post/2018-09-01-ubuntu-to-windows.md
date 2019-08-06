---
title: "Ubuntu/linuxmint远程桌面Windows"
date: 2018-09-01T12:23:14+08:00
layout: post
draft: false
tags: 
  - Ubuntu
categories:
  - Linux
---

Ubuntu/linuxmint远程桌面Windows

办公室的电脑使用Windows 10，有时候需要从家里的LinuxMint访问办公室的电脑，我百度了一下，发现很多人都是推荐rdesktop这个工具，但是这个工具是从命令行运行的，不是很喜欢，我想要带gui界面的，于是又搜索了一下Google，发现有人推荐**Remmina**。

这个工具很不错，支持多种协议，只要有合适的插件，就能各种远程，例如VNC,SSH等等。
<!--more-->
首先，打开软件管理程序，搜索`Remmina`

![](https://raw.githubusercontent.com/istek/img/master/imgRDP_Remmina.png)

在这一步要注意一下，我们需要安装Remmina和rdp插件两个包。因为Remmina默认支持的协议只有SFTP和SSH。

![](https://raw.githubusercontent.com/istek/img/master/imgRDP_RemminaPlugins.png)

安装完成之后，从开始菜单，搜索Remmina，运行程序。

![](https://raw.githubusercontent.com/istek/img/master/imgRDP_RemminaList.png)

打开程序后，添加一个远程连接，协议选择RDP，填写服务器IP，用户名，密码，桌面分辨率，色深，然后点击保存并连接，非常简单，对吗？

![](https://raw.githubusercontent.com/istek/img/master/imgRDP_RemminaConnect.png)

**PS：**
从家里连接办公室的台式机，台式机还是内网，怎么做到的？不是Teamviewer，而是FCN。感兴趣的朋友，可以到[这里](https://github.com/boywhp/fcn)了解一下FCN。
