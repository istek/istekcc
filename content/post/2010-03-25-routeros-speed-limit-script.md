---
title: RouterOS限速和限线程脚本
date: 2010-03-25T07:20:37+00:00
layout: post
categories:
  - 我的世界
tags:
  - RouterOS
---

RouterOS的限速和限制线程的脚本，作为备用。

ARP静态绑定

第一步，扫描IP
```
/tool

ip-scan interface=lan
```
lan是客户机所接的interface

执行一断时间后，记得Quit

第二步，ARP绑定
```
/ip arp
foreach i in=[find dynamic=yes] do={add copy-from=$i]}
```
<!--more-->
限线程脚本：
```
for aaa from 2 to 254 do={/ip firewall filter add chain=forward src-address=(192.168.0. . $aaa) protocol=tcp connection-limit=3,32 action=drop}
```

限速脚本：
```
for aaa from 2 to 254 do={/queue simple add name=(queue . $aaa) dst-address=(192.168.0. . $aaa) limit-at=0/0 max-limit=2048/1024}
```

说明：

脚本为一行不是两行；

aaa是变量,2 to 254是2~254,192.168.0. . $aaa是IP,上两句加起来是192.168.0.2~192.168.0.254的意思;

connection-limit=50是线程数这里为50;

max-limit=2000000/2000000是上行／下行;

使用:

WinBox-System-Scripts-＋

Name(脚本名程)

Source(脚本)

OK-选择要运行的脚本-Run Script

查看：

限线程：WinBox-IP-Firewall-Filter Rules(看是否已经填加进来)

限速：WinBox-Queues-Simple Queues(看是否已经填加进来)

双网切换脚本:
```
cncdown

     / ip route
     set [/ip route find comm=”CNC”] gateway 网通网关


cncup

     / ip route
     set [/ip route find comm=”CNC”] gateway 网通网关


maindown

     / ip route
     set [/ip route find comm=”main”] gateway 电信网关


mainup

     / ip route
     set [/ip route find comm=”main”] gateway 电信网关
```

网络通断
```
     / tool netwatch
     add host=电信网关 timeout=1000ms interval=10s up-script=mainup down-script=maindown \
     comment="" disabled=no
     add host=网通网关 timeout=1000ms interval=10s up-script=cncup down-script=cncdown \
     comment="" disabled=no
```

端口映射：

首先用WINBOX进入ROUTEROS，端口映射
```
    / ip firewall nat
     add chain=dstnat in-interface=pppoe-out1 protocol=tcp dst-port=80 action=dst-nat \
     to-addresses=192.168.1.10 to-ports=80 \
```

in-interface：选择公网接口 dst-port:选择需映射的端口

to-addresses: 输入需映射的主机地址 to-ports:输入主机需映射的端口