---
title: Linux RHEL5 双网卡绑定
date: 2012-12-26T19:08:47+00:00
layout: post
categories:
  - Linux
tags:
  - trunk
---

当需要将两块网卡trunk的时候，我们需要做双网卡绑定的操作，其实就是新建了一块虚拟网卡bond0，将两块实际网卡做了它的从设备。从而实现双网卡的trunk模式。trunk是端口汇聚的意思。

```
# cd /etc/sysconfig/network-scripts/
# cat /etc/sysconfig/network-scripts/ifcfg-bond0

DEVICE=bond0
BOOTPROTO=none
ONBOOT=yes
IPADDR=192.168.5.88
NETMASK=255.255.255.0
GATEWAY=192.168.5.1
USERCTL=no
```
<!--more-->
eth0和eth1是我们的实际网卡
```
# cat /etc/sysconfig/network-scripts/ifcfg-eth0

DEVICE=eth0
ONBOOT=yes
BOOTPROTO=none
MASTER=bond0
SLAVE=yes
USERCTL=no
```
```
# cat /etc/sysconfig/network-scripts/ifcfg-eth1

DEVICE=eth1
ONBOOT=yes
BOOTPROTO=none
MASTER=bond0
SLAVE=yes
USERCTL=no
```
```
# cat /etc/modprobe.conf

alias bond0 bonding
options bond0 miimon=100 mode=1
```
```
# cat /etc/rc.d/rc.local

ifenslave bond0 eth0 eth1
```

* mode=0 表示 load balancing (round-robin)为负载均衡方式，两块网卡都工作。
* mode=1 表示 fault-tolerance (active-backup)提供冗余功能，工作方式是主 从的工作方式,也就是说默认情况下只有一块网卡工作,另一块做备份。
* mode=2 表示 XOR policy 为平衡策略。此模式提供负载平衡和容错能力
* mode=3 表示 broadcast 为广播策略。此模式提供了容错能力
* mode=4 表示 IEEE 802.3ad Dynamic link aggregation 为 IEEE 802.3ad 为 动态链接聚合。该策略可以通过 xmit_hash_policy 选项从缺省的 XOR 策略改变到其他策略。
* mode=5 表示 Adaptive transmit load balancing 为适配器传输负载均衡。该 模式的必要条件：ethtool 支持获取每个 slave 的速率
* mode=6 表示 Adaptive load balancing 为适配器适应性负载均衡。该模式包含 了 balance-tlb 模式，同时加上针对 IPV4 流量的接收负载均衡(receive load   balance, rlb)，而且不需要任何 switch(交换机)的支持。

