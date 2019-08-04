---
title: 通过LiveCD修复GRUB2
date: 2009-08-27T20:45:24+00:00
layout: post
categories:
  - Linux
tags:
  - grub
---

昨天重新安装了Ubuntu，但是grub2出现问题，导致错误Error 15，压根进不了grub command line，没有办法，只好修复Grub2,无奈网上的内容多是修复Grub的，很少关于通过liveCD修复grub2的，在搜索google之后，发现了[该文](https://wiki.ubuntu.com/Grub2)，特地翻译出来，给有需要的朋友们使用。

首先，需要[Ubuntu LiveCD](http://www.ubuntu.com/getubuntu/download)，并且从LIVECD引导系统。

打开一个终端窗口，输入下面的命令
```
$ sudo fdisk -l
```

查看你的硬盘设备的map，记住Ubuntu在哪个设备上安装，比如我的Ubuntu安装于/dev/sdb1，而/dev/sda是Windows，下面的所有命令都是以这个例子为准的。之后，挂载/dev/sdb1设备.
```
$ sudo mount /dev/sdb1 /mnt
```

假如你的/boot分区使用了单独的分区，则还需要挂载这个分区，比如/dev/sdb2。一定要仔细查看过fdisk的输出内容，千万别搞错了。
```
$ sudo mount /dev/sdb2 /mnt/boot
```
<!--more-->
现在让我们来挂载其他的设备，
```
$ sudo mount –bind /dev/mnt/dev
```

现在chroot到你的系统。chroot进去的系统，可以执行很多命令，包括安装，删除软件，修改各种文件。它提供了与特殊的根文件系统交互的shell。
```
$ sudo chroot /mnt
```

应该是以root的权限chroot到你的根文件系统，之后就以root的权限执行命令，不需要sudo了。

现在应该编辑`/etc/default/grub`文件，使其适合你的系统。我只是打开看了一下，并没有做任何修改。
```
# vi /etc/default/grub
```

当完成编辑之后，需要创建配置文件.
```
# update-grub
```

将grub2安装到MBR，
```
# grub-install /dev/sdb
```

如果发现了任何错误信息，尝试一下 **grub-install –recheck /dev/sdb**

# grub-install –recheck /dev/sdb


按Ctrl+D退出Chroot环境，退回至LIVECD的终端窗口，接下来卸载所有刚才挂载的卷。
```
$ sudo umount /mnt/dev
$ sudo umount /mnt
```

然后重启系统，grub2应该就恢复正常了。