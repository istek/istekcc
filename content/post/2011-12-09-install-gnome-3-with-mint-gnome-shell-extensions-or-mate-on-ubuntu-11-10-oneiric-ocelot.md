---
title: Install GNOME 3 (With Mint GNOME Shell Extensions) Or Mate On Ubuntu 11.10 (Oneiric Ocelot)
date: 2011-12-09T22:57:45+00:00
layout: post
categories:
  - 我的世界
tags:
  - Ubuntu
---

> Version 1.0
> Author: Christian Schmalfeld <c [dot] schmalfeld [at] projektfarm [dot] de>
> Last edited 12/06/2011

This tutorial shows you how you can install the GNOME 3 desktop on Ubuntu 11.10 with the shell extensions that are used in the Linux Mint 12 release (Lisa) (these shell extensions are called Mint GNOME Shell Extensions – mgse). These shell extensions give GNOME 3 the look and feel of GNOME 2. For those who prefer GNOME 2 this tutorial shows how to install Mate from the Linux Mint repositories on Ubuntu 11.10. Mate is a GNOME 2 fork.

This document comes without warranty of any kind! I do not issue any guarantee that this will work for you!

### 1 Preliminary Note 序言

I have tried this on a desktop computer with an NVIDIA GeForce 8200. It should work the same way with all other NVIDIA graphics cards capable of 3D acceleration.

作者已经在他的Nidia 8200显卡的台式机上试验了。所有其他Nvidia支持3D加速的显卡都应该可以试试。
<!--more-->
### 2 Add Gnome Repository 增加Gnome源

To download the GNOME 3 package you first need to add the appropriate repository and update the software list. For that, open a terminal and enter following:

要下载Gnome3，首先应该添加相应的软件源，更新软件列表。打开终端，输入如下命令:
```
sudo add-apt-repository ppa:webupd8team/gnome3
sudo apt-get update
```

### 3 Install Gnome 3 安装Gnome3

To install the needed packages, enter following into the terminal:

安装需要的软件包，输入如下命令。
```
sudo apt-get install mgse-bottompanel mgse-menu mgse-windowlist gnome-shell-classic-systray gnome-tweak
```

After the installation, log out of your current session and click on the cog-wheel icon on the log-in screen to find the desktop options:

安装之后，退出你的帐号至登录界面，选择如图所示的选项：

![1](https://static.howtoforge.com/images/ubuntu_11.10_with_gnome3_and_mate/login_gnome.jpg)

Choose GNOME here to start with the full Gnome 3 desktop. This is what you desktop will look like afterwards:

选择Gnome，输入帐号密码登录后，启动完整的Gnome3桌面环境。下图是你应该看到的桌面样子：

![2](https://static.howtoforge.com/images/ubuntu_11.10_with_gnome3_and_mate/gnome_desktop_e.jpg)

![3](http://static.howtoforge.com/images/ubuntu_11.10_with_gnome3_and_mate/big/gnome_desktop_e.jpg)

![4](https://static.howtoforge.com/images/ubuntu_11.10_with_gnome3_and_mate/gnome_desktop_m.jpg)

To enable all the installed features, you first have to configure them with the `Gnome Tweak Tool`. Search for it in the `Activities` screen and click on Advanced Settings:

要启用所有已安装的特性，你需要使用GnomeTweakTool来配置他们，在活动中搜索这个程序，点击 高级设置：

![5](https://static.howtoforge.com/images/ubuntu_11.10_with_gnome3_and_mate/gnome-tweak-search.jpg)

Select Shell Extensions from the left panel and activate the features you want to have:

从左侧面板选择Shell扩展，启用你想要有的特性：

![6](https://static.howtoforge.com/images/ubuntu_11.10_with_gnome3_and_mate/gnome_tweak_tool.jpg)

### 4 Mate – Add Mint Main Repository Mate-增加Mint的主软件源

To install Mate, you first need to add the Linux Mint repository to your sources. To do that open your `sources.list` file by entering following:

要安装Mate，首先编辑你的source.list文件，增加linux mint软件源.
```
sudo gedit /etc/apt/sources.list
```

At the end of the file, add the following line:

在文件末尾，增加下面这一行：
```
>[…]
 deb http://packages.linuxmint.com/ lisa main upstream import
```

Save the file and close it afterwards. Before you import the key, update the package list:

保存文件，之后关闭文件。在导入key之前，更新软件包列表:
```
sudo apt-get update
```

Then import the Mint keyring…

之后导入Mint Keyring
```
sudo apt-get install linuxmint-keyring
```

… and update the package list once again:

然后更新软件包列表:
```
sudo apt-get update
```

### 5 Install Mate 安装Mate

To finally install Mate, enter:

最后安装Mate：
```
sudo apt-get install mint-meta-mate
```

After the installation has finished, log out of your current session and click on the cog-wheel icon of the log-in screen. Select `MATE` to start with the Mate desktop:

安装之后，退出你的帐号至登录界面，选择如图所示的选项：

![7](https://static.howtoforge.com/images/ubuntu_11.10_with_gnome3_and_mate/login_mate.jpg)

You will notice that with Mate, all the Mint theme-bound items were installed on your system:

你会注意到Mate，所有的mint主题绑定的项目都已经安装到你的系统里面了：

![8](https://static.howtoforge.com/images/ubuntu_11.10_with_gnome3_and_mate/mint_theme.jpg)

However this can be changed easily. Go to <span class="system">System > Preferences > Appearance</span>:

当然，可以轻松的更改。系统->首选项->外观:

![9](https://static.howtoforge.com/images/ubuntu_11.10_with_gnome3_and_mate/mint_theme_1.jpg)

On the `Theme` tab, select the `Ambiance` theme, which is the one Ubuntu 11.10 uses by default (don’t be confused by the displayed error message, everything will work as it’s supposed to):

在标题标签上，选择Ubuntu 11.10默认使用的主题Ambiance主题（别担心显示的错误信息，所有的东西都工作很正常）：

To change the wallpaper back to normal, select the Background tab and choose the one you like most. The default Ubuntu 11.10 wallpaper is also available on the list:

改变桌面背景至默认背景，选择背景标签卡，然后选择你最喜欢的桌面背景。Ubuntu 11.10的桌面背景在列表中也有:
