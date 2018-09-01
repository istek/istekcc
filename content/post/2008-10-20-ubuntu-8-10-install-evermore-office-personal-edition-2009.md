---
title: Ubuntu 8.10安装永中Office个人版2009
date: 2008-10-20T08:47:06+00:00
layout: post
categories:
  - 我的世界
tags:
  - Ubuntu
---

从网络上找到了8.04的安装方法，其实在8.10下面安装没有什么区别，不过我的桌面效果开了，安装对话框很正常，没有出现“白屏”和看不到字的情况。

1、在永中官方网站下载一个压缩包，这里需要注意的是一定要下载for linux版本，如果不幸下载了for win版本的，则以下步骤无效！

下载永中集成Office

下载:<http://218.90.147.70/EverMore/EIOPersonal/EIOffice>_Personal_Lin.tar.gz

2、将tar.gz包下载到主目录，然后解压缩

3、打开解压缩后的文件夹，看到n多文件，其中有setup安装文件，这些都不用管了，看看此时的路径为：`/home/用户名/EIOffice_Personal`

4、在终端中输入：`cd EIOffice_Personal`,然后回车
<!--more-->
5、在终端中输入：`sudo chmod +x setup`，然后回车，添加登录用户的执行权限

6、在终端中输入：`sudo ./setup` ，然后回车，

下面的解决办法先预留着，如果出现问题，可以使用。

在安装完成后，如果开启“桌面效果”启动也可能出现空界面的问题，下面是在网上搜索到的解决办法。
```
sudo gedit /usr/bin/eio
```
在第一行下加入
```
export AWT_TOOLKIT=MToolkit
```
