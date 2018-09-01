---
title: Octoshape支援多平台的P2P客户端
date: 2011-12-04T23:36:57+00:00
layout: post
categories:
  - 我的世界
tags:
  - linux
  - octoshape
---

当需要收听高清的香港电台，或者，收看斯洛文尼亚的电视台，怎么办？哈哈。用octoshape啊。目前支持的节目如下：

安装非常方便，点击[这里](http://www.octoshape.com/files/octosetup.exe)下载，然后运行安装，安装完后在开始菜单会添加一个程序组。之后，运行程序就可以了，会在系统栏出现一个图标。假如，现在想要收听香港电台普通话台，打开 <http://www.rthk.org.hk> 后，点击直播，听HD格式的节目即可。

也可以双击系统栏图标，会打开一个WEB页面，里面是所有的节目，点击一个之后根据提示操作，既可收听收看节目。

Linux系统下面：

最好安装Smplayer，openjava 6或者Sun-java。
```
sudo apt-get install smplayer openjdk-6-jdk
```
<!--more-->
下载Octoshape
```
wget http://www.octoshape.com/files/octosetup-linux_i386.bin>
```
然后给与文件可执行权限
```
chmod +x octosetup-linux_i386.bin
./octosetup-linux_i386.bin
```
安装完成后，会在当前目录新建一个octoshape的目录，里面有客户端2进制文件和一些配置文件。

收听节目时，执行
```
./octosharp-client -url:rthk.ch1
```
即可马上收听香港电台普通话台。我的setup.xml文件如下：
```
<config JavaExec=”/usr/lib/jvm/java-6-openjdk/jre/lib/i386/client/libjvm.so”
EulaAccepted=”true”
EulaId=”1105230.en”
EulaMethod=”installerwindow”
Language=”en”
Player=”0″
PlayerExec=”smplayer $url”
PlayerOutput=””
PlayMenu=”true”
PreferredTcpPorts=”443,8247,0″
PreferredUdpPorts=”8247,0″
Startup=”1″
TempID=”Gedelf\_Fikrafhas\_05227155″ />﻿
```

﻿**注意**：每个人的TempID是不同的，也是唯一的，请使用自己的tempID。