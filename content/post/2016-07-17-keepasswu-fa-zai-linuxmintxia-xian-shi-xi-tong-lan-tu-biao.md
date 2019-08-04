---
title: KeePass无法在LinuxMint下显示系统栏图标
date: 2016-07-17T06:34:40+00:00
layout: post
categories:
  - Linux
tags:
  - keepass
---
KeePass Password Safe是一个密码保管软件，现在的密码要求已经越来越复杂了，另外，我觉得自己的脑子也无法记住各种网站的密码，所以一直在使用这款软件用来保管密码。KeePass有一个非常好的方面，就是支持Linux，Windows，而且通过浏览器输入密码的KeePass插件都有的，很方便。

![1](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1468675078/yidwx97xubg5xm5pb9hy.png)

1.直接安装keePass
```
sudo apt-get install keepass2
```

2.安装完成后，直接运行keepass2，发现系统栏的图标是一个黑图标，而且无法右键，经过google，发现了这是一个[bug](https://sourceforge.net/p/keepass/bugs/1345/)。可以通过安装一个tray-icon的插件解决这个问题。
```
sudo add-apt-repository ppa:dlech/keepass2-plugins-beta
```

之后更新软件源，
```
sudo apt-get update
```

然后安装我们需要的插件，如果使用chrome，还需要安装keepasshttp，
```
sudo apt-get install keepass2-plugin-tray-icon keepass2-plugin-keepasshttp
```

3.现在重启keepass，是不是图标已经出来了？哈。