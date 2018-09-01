---
title: ArchLinux2012.09.07安装配置说明
date: 2012-09-11T16:35:35+00:00
layout: post
categories:
  - 我的世界
tags:
  - arch
---
最近心血来潮，想用一下大家都在说很好的一个Linux发行版，[archlinux](http://www.archlinux.org/)。目前在网易镜像上最新的ISO是2012.09.07版，这个版本的改变比较大，主要改变如下：

- First medium with Linux 3.5 (3.5.3)Linux 3.5.3的内核；
- The *script* boot parameter works again脚本引导参数（？）回归；
- When booting via PXE and NFS or NBD the ISO will be copied to RAM to ensure a more stable usage.当通过PXE和NFS或NBD引导的时候，ISO会被拷贝至内存保证更加稳定。
- The live medium contains *usb_modeswitch* and *wvdial* which e.g. allows to establish a network connection using an UMTS USB dongle。liveiso包括usb_modeswitch和wvdial，这样就允许使用UMTS USB适配器（3G网卡）建立网络链接。
- Furthermore the newest versions of *initscripts*, *systemd* and *netcfg* are included.另外，包括了最新的initscripts,systemd和netcfg。

目前大家可以在[网易镜像](http://mirrors.163.com/archlinux)，[搜狐镜像](http://mirrors.sohu.com/archlinux/)，[中科大](http://mirrors.ustc.edu.cn/archlinux/)，[北京交大](http://mirror.bjtu.edu.cn/archlinux/)，[电子科大](http://mirrors.stuhome.net/archlinux)等国内镜像网站下载到ISO。
<!--more-->
## 一、U盘安装方法这是目前大家使用最多的一种安装方式，方法很简单。

1、Windows

在windows下可以使用[windows image writer](https://launchpad.net/win32-image-writer/0.6/0.6/+download/win32diskimager-binary.zip)软件，选择正确的镜像文件，然后选择U盘盘符，最后点击write等待写入完成。

2、Linux

使用dd工具即可，具体命令如下:
```
dd if=/home/abc/archlinux.iso of=/dev/sdX bs=4M
```

注意：/dev/sdX是你的U盘设备文件，最好在做之前`fdisk -l`一下，看看U盘文件是哪个，比如`/dev/sdc`，不要后面的数字，因为要写入引导程式。

## 二、安装过程

1.使用U盘引导你的系统，选择需要安装的架构，I686还是x86_64。

2.分区，使用你熟悉的CLI分区工具，比如cfdisk，fdisk，parted工具。  分区其实不用那么纠结，每次上论坛，都看到很多很多朋友在问分区到底要分多大，分多少个，其实桌面应用，简单的分3个就可以了，即/分区，swap分区，home分区。因为我们平时上网，听歌，看电影，还是工作，都会将这些文件保存在自己的家目录里面，swap分区的分法是如果内存大于4G，即按1：1的比例分，如果小于4G，按内存的两倍分即可，根分区和home分区按自己的硬盘可用量分就行，比如/分区划分10G，/home划分20G，都是可以的。在本例中，我分了3个区，/分区10G,/home分区20G，swap分区4G，其设备文件分别为`/dev/sda7`,`/dev/sda8`和`/dev/sda9`。

3.分区后，就应该写入文件系统了，比如用ext4文件系统，命令如下：
```
mkfs.ext4 /dev/sda7
mkfs.ext4 /dev/sda8
mkswap /dev/sda9 && swapon /dev/sda9
```

4.挂载文件系统.
```
mount /dev/sda7 /mnt
mkdir /mnt/home
mount /dev/sda8 /mnt/home
```

5.链接网络.

我这里使用的是DHCP，就一条命令就可以了。连接无线网络和使用固定IP请看[此文](https://wiki.archlinux.org/index.php/Configuring_Network_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29)。
```
dhclient
```

无线网络：
```
wpa*passphase "无线ssid" "密码" >> /etc/wpa*supplicant.conf
wpa*supplicant -B -Dwext -i wlan0 -c /etc/wpa*supplicant.conf
```

6.设定pacman获得包的镜像服务器地址，在这里，我们使用网易镜像，编辑/etc/pacman.d/mirrorlistvi /etc/pacman.d/mirrorlist

加入如下部分(请注意大小写和架构类型)
```
Server = http://mirrors.163.com/archlinux/$repo/os/i686
```

7.安装基本系统和基本编译工具，此过程需要从服务器下载软件包，比较费时间，可以喝杯水，抽根烟，听音乐，呵呵。
```
pacstrap /mnt base base-devel
```

8.安装GRUB引导程序,对于BIOS的PC用如下命令：
```
pacstrap /mnt grub-bios
```

EFI的PC用如下命令
```
pacstrap /mnt grub-efi-i386
```

9.生成fstab文件，供引导系统时自动挂载。
```
genfstab -p /mnt >> /mnt/etc/fstab
```

10.chroot至/mnt,你会发现你的提示符变了。
```
arch-chroot /mnt
```

11.编辑主机名文件，给你的PC起个名字，比如china.
```
echo 'china' > /etc/hostname
```

12.编辑`/etc/locale.gen`文件，选择文字编码。
```
vi /etc/locale.gen
```
将如下字符集前的井号删除
```
en*US.UTF-8
zh*CN.GB2312
zh*CN.GB18030
zh*CN.UTF-8
zh_CN.GBK
```

13.生成新的字符集配置文件
```
locale-gen
```

14.创建引导linux内核的初始环境
```
mkinitcpio -p linux
```

15.安装grub引导程序至`/dev/sda`.
```
grub-install /dev/sda
```

16.生成grub配置文件，如果你还装了windows，建议先安装`os-prober`，然后再生成配置文件。
```
pacman -S os-prober [可选的]
grub-mkconfig -o /boot/grub/grub.cfg
```

17.修改root的密码
```
passwd
```

18.添加一个普通用户

```
useradd -d /home/abc -m abc
```

19.安装sudo，使abc可以使用sudo命令
```
pacman -S sudo
visudo
```

visudo是编辑sudo配置文件的命令。将如下语句前的#号删除，此句意思是允许wheel组的所有用户执行所有命令。
```
%wheel ALL=(ALL) ALL
```

将abc加入wheel组，audio组，video组，users组，games组，optical组，storage组，lp组
```
usermod -G audio,video,wheel,users,games,optical,storage,lp abc
```

20.安装bash自动补全包
```
pacman -S bash-completion
```

21.使用yaourt安装aur库软件
```
vi /etc/pacman.conf
```
加入如下内容
```
[archlinuxfr]
Server = http://repo.archlinux.fr/i686
```

之后安装Yaourt
```
pacman -Syyu
pacman -S yaourt
```

22.退出chroot，卸载/mnt.
```
exit
umount /mnt/home
umount /mnt
reboot
```

23.至此，一个没有X的archlinux已经安装在你的硬盘上了，接下来要做的就是安装X，桌面环境和其他日常用应用程序了。

