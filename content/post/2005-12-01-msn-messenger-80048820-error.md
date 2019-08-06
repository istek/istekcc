---
title: MSN Messenger 80048820错误
date: 2005-12-01T06:24:04+00:00
layout: post
categories:
  - Windows
---

我的MSN最近一段时间只要是在办公室里，就是无法连接网络，从网络上查了一下，主要存在的问题可能有：1，系统时间是否设置正确。2，可能没有注册系统的控件。据说是MSN Messenger 7.5的一个Bug，不知道是否是真实的。

对于系统注册控件：

在WINDOWS的”开始”菜单下选择”运行”,然后依次键入:

```
regsvr32 softpub.dll
regsvr32 mssip32.dll
regsvr32 intippki.dll
```