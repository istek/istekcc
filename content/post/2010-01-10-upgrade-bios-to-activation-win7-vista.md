---
title: 通过升级BIOS激活WIN VISTA SP2/WIN7
date: 2010-01-10T05:23:30+00:00
layout: post
categories:
  - 我的世界
tags:
  - windows
---

去年已经体验过WIN7 BETA了，最近想将自己的笔记本和台式机升级一下系统，毕竟守着WINXP是不现实的，时代在进步，我们这些IT人的思想也要开阔和放开些。beta的时候，使用ms提供的序列号使用了一些日子，说实话，界面很华丽，就是操作上与WINXP完全不同，毕竟内核已经提升至6.0了，可以说与WINXP不是一个档次的。

通过改BIOS的方式，可以使用OEM版的VISTA与WIN7，当然是纯粹的永久激活，这是我们这样做的初衷。它就是把SLIC2.1信息写进主板BIOS里，将你的电脑变成OME品牌机，之后再导入OEM证书和OEM KEY，使VISTA和Windows 7变成正版。

## 一、适用范围：

品牌机和兼容机。

## 二、工具：

1、Award_SLICTool 或 AMI_SLICTool 。 由于该软件使用.net编译，请安装Microsoft .NET Framework 2.0或以上版，否则会提示初始化错误！至于是使用Award还是AMI的SLIC Tool，完全取决于你的BIOS制造商。电脑开机硬件自检时，核实一下。

2、你的主板BIOS ROM程序，请到主板商网站下载。比如我的主板是Asus（华硕）M2N-X Plus，那么就去华硕中文站点下载最新的bios程序0507.rom。

3、对应OEM版本的证书。

4、华硕刷BIOS的Asus Update工具。题外话，华硕提供了在WINDOWS平台下直接刷BIOS的工具，非常方便，不用再像几年前进入DOS去做这些事情了。
<!--more-->
## 三、制作包含SLic2.1信息的BIOS ROM

1、运行AMI_SLICTool（SLIC）程序。

2、在原始BIOS那里选择我们下载的0507.rom，制造商会自动显示华硕ASUS，SLIC文件选择AMI_SLICTool里边自带的SLIC2.1文件夹中的HP.BIN，SLP选择SLP文件夹中的HP.TXT。校验文件选择CERT文件夹中的HP.XRM-MS。刷什么OEM版，就要在这里选对，比如我选择HP。

界面中的“方法”使用说明如下：

微星：仅超静2

讯景：仅超静2

华硕：超静3/动态/超静2（动态法仅适用个别型号）

映泰：超静3/2

昂达：超静2/3（超静3仅适用个别型号）

七彩虹：超静2/3/动态（超静3/动态法仅适用个别型号）

华擎：超静3/2

其他：超静2/3/动态（超静3/动态法仅作尝试）

3、点击“执行”，开始生成新的BIOS ROM程序。

之后会看到在文件夹中生成了一个文件0507_SLIC.ROM。这个文件就是我们所制作的含SLIC2.1信息的BIOS ROM文件。之后就可以使用Asus Update工具开始升级bios了。

## 四、升级主板的BIOS程序

1、安装Asus Update程序。

2、运行Asus Update程序，选择从档案进行升级。

![zol](https://img8.zol.com.cn/bbs/upload/4472/4471435_0500.gif)

3、点击NEXT，选择我们刚才生成的0507_SLIC.ROM文件。

4、核对信息是否正确，如果正确，则点击“Flash”。

5、等待清除ROM、升级ROM、核对ROM三个进度条都到100%就完成了，系统提示需要重启。

6、重启后需要重新进入BIOS设置一下。SAVE & EXIT。

如果一切正常的话，用EVEREST打开主板-ACPI，你就可以看到SLIC信息已经在那里了。如果是安装各大品牌的oem系统，接下来进行光盘安装，装好后已自动激活。

OK，升级BIOS的工作就完成了，接下来，我们开始激活项目。

激活VISTA SP2/WIN7：

1、安装VISTA SP2/WIN7。我个人安装的是MSDN VISTA SP2简体中文。

2、关闭UAC。

UAC(User Account Control，用户帐户控制)是微软为提高系统安全而在Windows Vista中引入的新技术，它要求用户在执行可能会影响计算机运行的操作或执行更改影响其他用户的设置的操作之前，提供权限或管理员‌密码。通过在这些操作启动前对其进行验证，UAC 可以帮助防止恶意软件和间谍软件在未经许可的情况下在计算机上进行安装或对计算机进行更改。

控制面板——用户帐户——打开或关闭“用户帐户控制”——取消“使用用户帐户控制（UAC）帮助保护您的计算机”的复选框，然后重启。

3、运行激活工具。

共执行2次！

第一次，按所升级的OEM版本导入证书。本例中，是J，执行完成后自动关闭。

第二次，导入安装的版本的序列号。本例中，安装的是VISTA SP2 Business，输入5。

重启系统，查看是否激活。

所有使用到的工具，请到[我的网盘](http://cid-574f8e37bafc4791.skydrive.live.com/browse.aspx/.Public/vista)下载。