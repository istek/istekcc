---
title: 黑莓MDS激活服务器搭建步骤教程/MDS企业激活服务器的搭建方法
date: 2010-09-25T21:42:35+00:00
layout: post
categories:
  - windows
tags:
  - 黑莓
---

**BlackBerry开发平台应用：关于架设黑莓MDS激活服务器的操作**

1.首先下载两个必须文件：

* BlackBerry_Email_MDS_4.1.0-4 下载地址：[这里下载](http://down.maxpda.com/blackberry/BlackBerry_Email_MDS_4.1.4.html)
* jdk-6u10-rc2-bin-b32-windows-i586-p-12_sep_2008.exe

2.安装JDK（java开发工具包，包含JRE(JAVA运行环境))

3.然后安装MDS模拟器，安装时都要使用默认路径（安装过程中不要动设置就可以了）例如：C:\ProgramFiles\ResearchInMotion\BlackBerryEmailandMDSServicesSimulators4.1.0\MDS和C:\ProgramFiles\Java\jre6

4.接下来点击`C:\Program Files\Research In Motion\BlackBerry Email and MDS Services Simulators 4.1.4\MDS中的RUN.bat`，如果正常的话应该出现一系列的dos窗口信息(如下图所示，如果出现了这个窗口就不用做下面4.1和4.2的设置了):
<!--more-->
如果窗口是一闪而过没有任何提示，那么就需要做以下的设置：其实就是设置一下环境变量:

右键编辑MDS目录下的`run.bat`文件，其中的JAVA_HOME就是我们要设置的环境变量，把它指向我们JRE目录`C:\Program Files\Java\jre6`，如果在其他目录，自行设置。

4.1. BlackBerry开发中设置完环境变量后，将`run.bat`中修改为`%JAVA_HOME%\bin\java=C:\Program Files\Java\jre6\bin\java`

4.2. 设置环境变量的方法：右键点击“我的电脑”-“属性”-”高级”-”环境变量”

将administrator环境变量和系统环境变量里“新建”，变量名：`JAVA_HOME`变量值：`C:\Program Files\Java\jre6`(其他路径自行设置),再运行run.bat即可。

  1. 用TcpView查看网络连接(本步骤可忽略)

  2. 看到8080和19781端口已经开放

  3. BlackBerry开发测试：在浏览器中输入：`http://localhost:8080`就可以打开服务器页面。

  4. 使用外网IP即可进行激活。(本步骤在手机内进行)