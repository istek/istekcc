---
title: 去除烦人的迅雷广告
date: 2010-01-17T20:33:10+00:00
layout: post
categories:
  - windows
tags:
  - 迅雷
---

在迅雷的窗体里面，堆满了不少的广告，还有右下角的，迅雷提供了关闭按钮，但是，一点点击关闭广告按钮，就会跳出一个注册和登陆窗口，高兴的点击确定，发现，不是VIP用户就不能去除广告……

今天这个技巧，仅供大家技术交流啦，不做VIP，也不打任何补丁，咱们自己动手去掉迅雷的广告（<span style="color: #ff0000;"><strong>主面板广告和新建下载窗体的广告</strong></span>），当然，**<span style="color: #ff0000;">首先前提，你起码注册了普通的迅雷账号</span>**，能够登陆才行。

**具体操作步骤：**

1、退出运行中的迅雷；

2、找到资源管理器里面找到文件夹：

Vista、Win7和Win2008等系统用户请找到：`C:\Users\Public\Thunder Network\User\_您的迅雷ID_\`

XP、2003等系统用户请找到：`C:\Documents and Settings\All Users\Application Data\Thunder Network\User\_您的迅雷ID_\ `

3、看一下该目录下是否有“vipsettings.ini”文件，没有就新建一个。

4、向文件里输入如下内容：
<!--more-->
///////// 以下为 vipsettings.ini 中的内容
```
[VIP_SETTING]

THUNDER_MINISITE=0
SKIN_TYPE=1
SKIN_NAME=default
FIRST_LOGIN=0
MAIN_AD=0
NEW_TASK_PANEL_AD=0
XUNLEI_TIP=0
WELCOME_PANEL=0
VIP_CFG_SYNCHRO=0
```
///////// 以上为 vipsettings.ini 中的内容

各设置项特别解释如下：
```
[VIP_SETTING]

THUNDER_MINISITE=0 //迅雷资讯，1是开启，0是关闭
SKIN_TYPE=1 //皮肤类型，有两类，非别是1和2，2是会员皮肤
SKIN_NAME=default //皮肤名
FIRST_LOGIN=0
MAIN_AD=0 //主界面广告，1是开启，0是关闭
NEW_TASK_PANEL_AD=0 //新建面板广告，1是开启，0是关闭
XUNLEI_TIP=0 //影视快讯，1是开启，0是关闭
WELCOME_PANEL=0 //欢迎界面，好像是会员有效的。
VIP_CFG_SYNCHRO=0
```
5、保存`vipsettings.ini` ，**然后将该文件的属性设为只读（此步必需！！）**。

OK！重新启动迅雷之后，是不是干净多了？
