---
title: EVA4400的array controller
date: 2008-07-31T06:31:18+00:00
layout: post
categories:
  - Linux
tags:
  - hp
---

最近在做浦发银行的安装，碰到了几个菜的棘手问题。SMA上安装了HBA卡的storport驱动，但是发现EVA HSV 300 ARRAY CONTROLLER在设备管理器中仍然是other device，上面一个questions mark，我就很晕，前些日子，做铁路局的安装，使用的是同一个驱动，都没有出现这个问题，为什么这次就出现了呢，开了一个内部call，rc说是驱动用得不合适，我很郁闷，于是他告诉我去下载对应操作系统的驱动，另外还要安装一个Windows的补丁，就可以了。

另外，安装`command view for eva`时，出现了一个BLM Error的错误，说什么XML COMMAND FAILED，咨询了一下rc，rc说Windows系统不好trouble shooting，建议重新安装os，我很无奈，晚上临下班的那会，找了半天盒子都没有翻出来一张system recovery DVD，明天要是找不到，我就要死了！唉。

关于A8003A HP StorageWorks FC2242SR的文档，可以下载该[PDF](http://docs.hp.com/en/9325/sw_a8002a_a8003a_ig.pdf)。

关于驱动，先打开这个[页面](http://h20000.www2.hp.com/bizsupport/TechSupport/DriverDownload.jsp?prodNameId=1143907&lang=en&cc=us&prodTypeId=12169&prodSeriesId=1809835&taskId=135)，然后选择适当的OS，下载storport HBA driver。比如这次SMA的系统是Windows Storage Server 2003 R2,那么就选择[<span style="color: #003366;">Microsoft Windows Server 2003 for 64-bit Extended Systems</span>](http://h20000.www2.hp.com/bizsupport/TechSupport/SoftwareDescription.jsp?lang=en&cc=us&prodTypeId=12169&prodSeriesId=1809835&prodNameId=1143907&swEnvOID=1113&swLang=8&mode=2&taskId=135&swItem=co-61255-1)，然后下载驱动版本是2.01a4，release at 16 Jun 2008的版本，具体可以阅读[Release notes](http://h20000.www2.hp.com/bizsupport/TechSupport/SoftwareDescription.jsp?lang=en&cc=us&prodTypeId=12169&prodSeriesId=1809835&swItem=co-61255-1&prodNameId=1143907&swEnvOID=1113&swLang=8&taskId=135&mode=4&idx=0)，查看各个os的支持情况，尤其是注意下面的这段话：
<!--more-->
> Driver installation with HP Smart Component requires a minimum of Windows Server 2003 SP2 or SP1 with update KB932755. <strong>Apply the Microsoft STORport update (KB932755) before installing or upgrading to this version of the STORport driver</strong>. For boot installations, Windows Server 2003 SP2 install image is required, followed by the KB update.

所以在安装驱动之前，必须安装KB932755的补丁。((下次安装需要注意))