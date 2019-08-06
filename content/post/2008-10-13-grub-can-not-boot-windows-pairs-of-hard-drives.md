---
title: Grub不能引导Windows双硬盘
date: 2008-10-13T06:05:30+00:00
layout: post
categories:
  - Linux
tags:
  - grub
---

我的系统两块硬盘，Primary Slave是一块IDE的硬盘，安装了Ubuntu 8.04，还有

一块SATA硬盘，安装了WINXP。前两天重新安装后，发现Ubuntu的grub竟然不能引导我的WINDOWS XP，这让我很纳闷，搜索了，但是没有找到答案。后来在社区有位兄弟给了我solution。

fdisk -l的显示如下:
```
$ sudo fdisk -l
Disk /dev/sda: 120.0 GB, 120034123776 bytes 255 heads, 63 sectors/track, 14593 cylinders Units = cylinders of 16065 * 512 = 8225280 bytes Disk identifier: 0xf0b1ebb0
Device Boot Start End Blocks Id System
/dev/sda1 * 1 243 1951866 82 Linux swap / Solaris
/dev/sda2 244 14593 115266375 5 Extended /dev/sda5 244 14593 115266343+ 83 Linux

Disk /dev/sdb: 250.0 GB, 250059350016 bytes 255 heads, 63 sectors/track, 30401 cylinders Units = cylinders of 16065 * 512 = 8225280 bytes Disk identifier: 0x1ba41ba3
Device Boot Start End Blocks Id System
/dev/sdb1 * 1 3824 30716248+ 7 HPFS/NTFS
/dev/sdb2 3825 30401 213479752+ f W95 Ext'd (LBA)
/dev/sdb5 3825 14278 83971723+ 7 HPFS/NTFS
/dev/sdb6 14279 30401 129507966 7 HPFS/NTFS *
```
<!--more-->
menu.lst文件的Ubuntu和Windows部分的代码如下:
```
title Ubuntu 8.04.1, kernel 2.6.24-21-generic
root (hd0,4)
kernel /boot/vmlinuz-2.6.24-21-generic root=UUID=7fab4bcf-a237-42b0-b51f-254d42fea748 ro splash noapic locale=zh_CN vga=792 quiet
initrd /boot/initrd.img-2.6.24-21-generic quiet

title Microsoft Windows XP Professional
rootnoverity (hd1,0)
savedefault makeactive
chainloader +1
boot*
```

解决办法如下(共两种办法)：

**第一法:**
```
> map (hd1) (hd0)
> rootnoverify (hd1,0)
> makeactive
> chainloader +1 >*
```

**第二法:**
```
> find --set-root /yykXP.ck_
> chainloader /ntldr
abcxp.ck_ 把这个放在你的XP区的根下.(XP的那里就放在那个根下).
```

嘿嘿，这下两个系统都能进去了。当然，还能使用WINDOWS XP的BOOT.INI来加载Ubuntu。方法如下：
```
$ sudo dd if=/dev/sda1 of=/media/winc/ubuntu bs=512 count=1
```

该命令是将sda1的盘头的512字节读出来，保存为ubuntu文件至C盘根下。

接着修改Windows的boot.ini文件，在文件最后加入`c:\ubuntu="Ubuntu"`就可以了，整个文件如下:
```
[boot loader]
timeout=5
default=multi(0)disk(0)rdisk(0)partition(1)\WINDOWS [operating systems] multi(0)disk(0)rdisk(0)partition(1)\WINDOWS="Microsoft Windows XP Professional" /noexecute=optin /fastdetect
c:\ubuntu="Ubuntu 8.04"
```

方法多种多样，我就是觉得Grub比较好。另外，Ubuntu 8.10就要发布了，很期待。
