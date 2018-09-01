---
title: 配置NFS共享的要点
date: 2017-12-12 22:53:30
layout: post
tags: 
  - nfs
  - centos
---
# NFS配置要点

## NFS的常用目录

* /etc/exports                           NFS服务的主要配置文件
* /usr/sbin/exportfs                   NFS服务的管理命令
* /usr/sbin/showmount              客户端的查看命令
* /var/lib/nfs/etab                      记录NFS分享出来的目录的完整权限设定值
* /var/lib/nfs/xtab                      记录曾经登录过的客户端信息

## 需要启动的服务

rhel 6以上版本：rpcbind，nfs
rhel 6以下版本：portmap ，nfs
<!--more-->

## 131和132挂载过程

### 131:

`/etc/fstab`文件内容如下：

```bash
#
# /etc/fstab
# Created by anaconda on Mon Mar  6 02:16:59 2017
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/rootvg-lv_root /                       ext4    defaults        1 1
UUID=52c40687-62c3-42ef-ac89-4d7dce6c436a /boot    ext4    defaults        1 2
/dev/mapper/rootvg-lv_swap swap                    swap    defaults        0 0
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0
/dev/mapper/vg-data     /tas            ext4    defaults        0       0
```

配置nfs服务

```bash
[root@c5-cw-tas-web1 etc]# chkconfig rpcbind on
[root@c5-cw-tas-web1 etc]# chkconfig nfs on
[root@c5-cw-tas-web1 etc]# cat exports
/tas/upload     10.19.111.0/24(rw)
[root@c5-cw-tas-web1 tas]# service rpcbind restart
Stopping rpcbind:                                          [  OK  ]
Starting rpcbind:                                          [  OK  ]
[root@c5-cw-tas-web1 tas]# service nfs restart
Shutting down NFS daemon:                                  [  OK  ]
Shutting down NFS mountd:                                  [  OK  ]
Shutting down NFS quotas:                                  [  OK  ]
Shutting down RPC idmapd:                                  [  OK  ]
Starting NFS services:                                     [  OK  ]
Starting NFS quotas:                                       [  OK  ]
Starting NFS mountd:                                       [  OK  ]
Starting NFS daemon:                                       [  OK  ]
Starting RPC idmapd:                                       [  OK  ]
[root@c5-cw-tas-web1 tas]# exportfs
/tas/upload     10.19.111.0/24
```

### 132:

#### 手动挂载

```bash
mount 10.19.111.131:/tas/upload /tas/upload
mount -t nfs -o soft 192.168.100.110:/u01 /u02/test01  
```

#### 自动挂载

```bash
#
# /etc/fstab
# Created by anaconda on Mon Mar  6 02:16:59 2017
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/rootvg-lv_root /                       ext4    defaults        1 1
UUID=52c40687-62c3-42ef-ac89-4d7dce6c436a /boot                   ext4    defaults        1 2
/dev/mapper/rootvg-lv_swap swap                    swap    defaults        0 0
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0
/dev/mapper/vg-data     /tas                    ext4    defaults        0       0
10.19.111.131:/tas/upload       /tas/upload     nfs     defaults        0       0
```

## 修改端口及配置防火墙

### 修改端口

rpcbind在NFS服务启动的时候给每一个NFS服务分配了一个动态的端口，如这些服务MOUNTD_PORT、 STATD_PORT、 LOCKD_TCPPORT、 LOCKD_UDPPORT。如何才能让NFS client在使用 iptables时可以正常使用NFS服务呢？办法就是指定将上述服务的运行端口，然后在iptables中发布。相关的配置文件为 /etc/sysconfig/nfs 。

要使用iptables来控制NFS，需静态指定端口号让rpcbind（portmap）调用。NFS服务启用时会检查/etc/sysconfig/nfs文件。在此文件下来指定mountd,statd,lockd,rquotad端口号。

```bash
RQUOTAD_PORT=10001
LOCKD_TCPPORT=10002
LOCKD_UDPPORT=10002
MOUNTD_PORT=10003
STATD_PORT=10004
```

`/etc/sysconfig/nfs`默认安装的时候可能会不存在，5.1以上版本除外。可以手工创建此文件编写以上内容。
设定后需重启服务

```bash
service nfslock restart
service nfs restart
```

使用命令查看端口

```bash
#rpcinfo -p
program vers proto port
100000 2 tcp 111 portmapper
100000 2 udp 111 portmapper
100024 1 udp 10004 status
100024 1 tcp 10004 status
100011 1 udp 10001 rquotad
100011 2 udp 10001 rquotad
100011 1 tcp 10001 rquotad
100011 2 tcp 10001 rquotad
100003 2 udp 2049 nfs
100003 3 udp 2049 nfs
100003 4 udp 2049 nfs
100021 1 udp 10002 nlockmgr
100021 3 udp 10002 nlockmgr
100021 4 udp 10002 nlockmgr
100021 1 tcp 10002 nlockmgr
100021 3 tcp 10002 nlockmgr
100021 4 tcp 10002 nlockmgr
100003 2 tcp 2049 nfs
100003 3 tcp 2049 nfs
100003 4 tcp 2049 nfs
100005 1 udp 10003 mountd
100005 1 tcp 10003 mountd
100005 2 udp 10003 mountd
100005 2 tcp 10003 mountd
100005 3 udp 10003 mountd
100005 3 tcp 10003 mountd
```

### 配置防火墙规则

接下来就是配置iptables规则了

```bash
iptables -A INPUT -p tcp --dport 111 -j ACCEPT
iptables -A INPUT -p udp --dport 111 -j ACCEPT
iptables -A INPUT -p tcp --dport 2049 -j ACCEPT
iptables -A INPUT -p udp --dport 2049 -j ACCEPT
iptables -A INPUT -p tcp --dport 10001:10004 -j ACCEPT
iptables -A INPUT -p udp --dport 10001:10004 -j ACCEPT
```

重新挂载NFS

```bash
mount -t nfs -o nolock 172.16.10.254:/opt /mnt
```
