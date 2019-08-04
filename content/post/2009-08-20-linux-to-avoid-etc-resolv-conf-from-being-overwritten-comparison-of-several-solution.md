---
title: Linux 中避免 /etc/resolv.conf 被改写 + 几种解决办法的比较
date: 2009-08-20T21:01:15+00:00
layout: post
categories:
  - Linux
tags:
  - resolv.conf
---

最近又遇到`/etc/resolv.conf`会定时被改写掉的问题了，这回是在FreeBSD中！

在 LAN via NAT 或是 PPPoE 的环境中上Internet，我们总得在`resolv.conf`中指定 DNS server，而这里的 DNS server 是通过 DHCP server 自动获取的，如果不能正确获得则需要手动把 ISP 给出的 DNS server 加入。可有时候一些“聪明”的程序总是会跳出来搞些小动作，把我们自己写入的设定换掉，而且还是轮询的，你再改，没有过几分钟就又被改掉。

之前遇到过的情况有（DNS server 抓不对，且会被改写！）：

1. Ubuntu 6.10 (Edgy) 通过 ppp 拨号时，只抓到 ADSL Modem DHCP server IP.
2. Ubuntu 7.04 (Feisty) 那个可恶的network-manager程序会定时抓 ADSL Modem DHCP server IP.
3. VMWare 中的 Guest OS 以 NAT 网络 获取IP的时候，只抓到NAT DHCP server IP.

于是有很多人都跳出来问一个问题：“如何避免 resolv.conf 被修改、改写和覆写等等？“

然而对于UNIX这样灵活的系统，解法也有很多，我们一一看来，哪个粗糙，哪个优雅！
<!--more-->
**解法一**：

修改`/sbin/dhclient-script`，注释掉对`resolv.conf`进行操作的地方.

Linux ：

把`make_resolv_conf`这个函数的调用都给注释掉！

FreeBSD：

把`resolv.conf`所在的行都给注释掉！

**解法二**：

强制修改`resolv.conf`的文件flag，设定为不可被更改.

Linux：
```
chattr +i /etc/resolv.conf
```

FreeBSD：
```
chflags schg /etc/resolv.conf
```

**解法三**：

变更DHCP Client的配置文件，在特定的环节追加我们的设定.

Linux (Ubuntu Dist.)：

在`/etc/dhcp3/dhclient.conf`中增加如下设定：
```
prepend domain-name-servers <ip1>, <ip2>;
```

FreeBSD：

在`/etc/dhclient.conf`中增加如下设定：
```
prepend domain-name-servers <ip1>, <ip2>;
```

就我自己看来：解法一属于粗糙，解法二属于暴力，解法三才是好办法！

解法三不会变更系统框架，还是原来执行流程。我们只是在默认的prepend环节，多增加prepend的内容，让DHCP Client 在得到DNS server信息后，把我们所需要的配置追加在前面。