---
title: 通过VNC远程安装CentOS或Fedora
date: 2013-09-17T05:25:19+00:00
layout: post
categories:
  - Linux
tags:
  - vnc
---
有时候，我们会遇到这种情况，就是打算给服务器重新安装操作系统，要么机房离自己很远，要么就是你去了机房，发现机房里面没有键盘，没有显示器，我上周去机房安装操作系统，遇到的就是第二种情况，很郁闷。还好，曾经在google上搜索过远程安装CentOS的文章，所以正好拿生产服务器测试了一下，可以说，能达到远程安装操作系统的目的，我安装的是CentOS 6.4 x64，完全没有障碍，要求就是你的服务器必须得有给力的网络条件以及能访问公网。

在安装之前，我们需要几个信息：

1. 服务器的IP地址
2. 子网掩码
3. 网关和DNS
4. 网卡MAC地址（重要！我们要靠它来做IP指定）

<!--more-->

获得以上信息的命令：
```
ifconfig
ip route show
cat /etc/sysconfig/network-scripts/ifcfg-eth0
cat /etc/sysconfig/network
cat /etc/resolv.conf
```
下面是示例信息：

* IP:172.16.5.227
* NETMASK:255.255.255.0
* GATEWAY:172.16.5.1  DNS:8.8.8.8
* MAC:52:54:00:4a:25:b5

## 一、下载内核文件及initrd镜像文件

CentOS
```
wget -O /boot/vmlinuz_remote http://mirrors.sohu.com/centos/6.4/os/x86_64/isolinux/vmlinuz
wget -O /boot/initrd_remote.img http://mirrors.sohu.com/centos/6.4/os/x86_64/isolinux/initrd.img
```

Fedora
```
wget -O /boot/vmlinuz_remote http://mirrors.sohu.com/fedora/releases/19/Fedora/x86_64/os/isolinux/vmlinuz
wget -O /boot/initrd_remote.img http://mirrors.sohu.com/fedora/releases/19/Fedora/x86_64/os/isolinux/initrd.img
```

## 二、修改Grub.conf启动文件

CentOS
```
title Remote Install
root (hd0,0)
kernel /vmlinuz_remote lang=en_US keymap=us method=http://mirrors.sohu.com/centos/6.4/os/x86_64/ vnc vncpassword=1234567 ip=172.15.5.227 netmask=255.255.255.0 gateway=172.16.5.1 dns=8.8.8.8 noselinux ksdevice=52:54:00:4a:25:b5 headless xfs panic=120
initrd /initrd_remote.img
```

Fedora
```
title Remote Install
root (hd0,0)
kernel /vmlinuz_remote lang=en_US keymap=us method=http://mirrors.sohu.com/fedora/releases/19/Fedora/x86_64/os/ vnc vncpassword=1234567 ip=172.16.5.227 netmask=255.255.255.0 gateway=172.16.5.1 dns=8.8.8.8 noselinux ksdevice=52:54:00:4a:25:b5 headless xfs panic=120
initrd /initrd_remote.img
```

注意：这里要非常小心，kernel是一行哦，别给弄成两行了，这里配置不好的话，导致的结果就是服务器引导不起来，必须得去机房按服务器的开机按钮，我就吃了这个亏，所以一定要注意。vpcpassword是VNC密码，你可以设置为别的。其他字段的设置都很简单。

修改好后，就需要设置grub.conf中项目的引导顺序了。使用如下命令：
```
echo 'savedefault --default=1 --once' | grub --batch
```

## 三、重启服务器，使用VNC软件连接

OK，如果你确定上面的设置，你都搞定了，那么OK，你现在可以重启服务器了，重启后，你可以尝试ping服务器，什么时候能ping通就可以用像[TightVNC](http://www.tightvnc.com/)类的软件直接连接了。

方法是打开VNC软件，点连接，填入`IP:1`进行连接(如 192.168.0.15:1)，顺利的话，会提示你输入密码，这时候，你输入密码，就可以打开CentOS或者Fedora的安装界面了。从IP地址能ping通到能连接VNC，可能需要一点时间，别着急，要有耐心。

操作系统的安装，我就再不说了，都是轻车熟路的事情。

