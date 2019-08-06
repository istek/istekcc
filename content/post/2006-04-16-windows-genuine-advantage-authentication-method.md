---
title: Windows正版增值验证方法
date: 2006-04-16T17:41:18+00:00
layout: post
categories:
  - Windows
---

今天重新安装了电脑，更新补丁的时候发现需要正版增值验证，很讨厌的东西，我记得以前从网站上看到过这方面的修改方法，特地找出来，供大家学习。赫赫。希望大家喜欢哦。

打开我的电脑,工具-文件夹选项-查看-将隐藏受保护的操作系统文件的勾去掉,并将显示所有文件和文件夹选上

找到`C:\Documents and Settings\All Users\Application Data\Windows Genuine Advantage\data\data.dat`文件。 具体哪个盘,根据你系统装在哪个盘里.把它的属性改成”只读”.

验证就可以通过了!

如果你的计算机在自动更新的时候出现 正在查找适用于您计算机的最新更新程序 界面很久没有反应，然后就出现[错误号: 0x80072EE2] 可以使用以下的方法解决：

启动到安全模式，将`C:\WINDOWS\SoftwareDistribution`和`C:\WINDOWS\System32\SoftwareDistribution`目录下所有的文件都删除掉。

重启再进行在线升级就OK了。