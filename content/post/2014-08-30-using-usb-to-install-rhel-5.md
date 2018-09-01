---
title: 使用U盘安装RHEL 5.5
date: 2014-08-30T06:00:28+00:00
layout: post
categories:
  - 我的世界
tags:
  - RHEL
---

今天成功在**DELL Optiplex 360**上安装rhel 5.5，记录如下，具体步骤不是很清晰了，所以步骤不是很详细。

![RHEL 5](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449214648/1449229713_oa9hhl.jpg?w=720)
<!--more-->
1. 下载[rhel 5.5](http://mirrors.sohu.com/RHEL/AS5U5/i386/rhel-server-5.5-i386-dvd.iso)，选择适合你的版本有i386、x86_64。
2. 使用UltraISO打开`rhel-server-5.5-i386-dvd.iso`里面的`images/boot.iso`文件。点UltraISO【启动】菜单，【写入硬盘映像…】，写入方式可以是USB-HDD或者USB-HDD+，点【写入】。`boot.iso`包含了我们启动所需要的两个关键文件：`isolinux/vmlinuz`和`isolinux/initrd.img`。
3. 复制`rhel-server-5.5-i386-dvd.iso`到U盘根目录。
4. 重启系统，F12（有些机器不一样）进入选择启动介质界面。选择USB device。就启动到了linux rescue模式。有多种方式供我们选择，输入`linux askmethod`，回车。
5. 接下来就是选择语言，键盘布局，然后接下来是最关键是选择安装方式，有NFS/CDROME/HardDisk/FTP/HTTP。选择HardDisk。然后列出来很多分区供我们选择（/dev/sda1,/dev/sda2,/dev/sda3,/dev/sdb4），一般sda指硬盘，sdb是我们插上去的u盘。选择/dev/sdb4，就查找到u盘上的iso镜像。接下来是分区，设置密码、防火墙、SELinux、网卡配置、选择软件包等。。这些不详讲。关键一步，选择grub安装在那个地方，默认只有/dev/sdb，不能安装grub到u盘，否则u盘grub被覆盖了就完蛋鸟。选择`no boot loader will be installed`。等待安装完成。
6. 安装完了，继续F12进u盘，输入`chroot /mnt/sysimage`，挂载root分区。
7. fdisk -l 查看继续上的分区情况。一般有swap，根分区，/boot分区等。假设硬盘是/dev/sda，u盘是/dev/sdb。、dev/sda1是/boot，dev/sda2是/，dev/sda3是swap。
```
# grub-install /dev/sda
grub> find /grub/stage1 (或者/boot/grub/stage1)
grub> root (hd0,0) (这个是/boot分区，对应/boot，如果没有单独划分/boot分区，这里root指定/分区)
grub> setup (hd0)
grub> quit
```
这样grub就安装在硬盘上了，还需要新建一个grub菜单。`vi /boot/grub/grub.conf`
```
default=0
timeout=5
splashimage=(hd0,0)/grub/splash.xpm.gz
title rhel 5.5
root (hd0,0)
kernel /vmlinuz-2.6.18-194.el5PAE ro root=LABEL=/ rhgb quiet
initrd /initrd-2.6.18-194.el5PAE.img
```
修改权限：
```
chmod 600 /boot/grub/grub.conf
```
软链接：
```
ln -s /boot/grub/grub.conf /boot/grub/menu.lst
```

第一次启动后的设置，不详讲。

