---
title: 在Windows 2003下建立NTP服务
date: 2009-02-05T22:58:17+00:00
layout: post
categories:
  - Windows
tags:
  - ntp
---

在Windows 2003 Server系统下配置成功。

1.默认情况下，独立服务器WINDOWS SERVER 2003 是作为NTP客户端工作的 ，所以必须通过修改注册表，以使系统作为NTP服务器运行。**工作之前请先备份注册表文件**。

2.修改以下选项的键值`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W32Time\TimeProviders\NtpServer`内的「Enabled」设定为`1`，打开NTP服务器功能（默认是不开启NTP Server服务，除非服务器升级成为域控制器）

3.修改以下键值`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W32Time\Config\AnnounceFlags`设定为十六进制的5．该设定强制主机将它自身宣布为可靠的时间源，从而使用内置的互补金属氧化物半导体 (CMOS) 时钟。（设定好后就要确定本机的电池要耐用了，做成时间服务器，时间一出错就头大了，呵呵）　如果要采用外面的时间服务器就用默认的a值即可．

４．我这边的服务器同步用外部服务器，地址为210.72.145.44 (中国国家授时中心）

５．重启Win32Time服务： `net stop w32Time && net start W32Time`

６．至此，已完成服务器端设定．
<!--more-->
７．客户端的设定更改注册表即可．
```
HKEY\_LOCAL\_MACHINE\SYSTEM\CurrentControlSet\Services\W32Time\TimeProviders\NtpClient

SpecialPollInterval　　　　　值修改成十进制43200　（单位为秒，43200为12小时）

SpecialPollTimeRemaining　　值修改成［时间同步服务器］,0　如：192.168.1.1,0
```
８．完成．