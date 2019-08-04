---
title: "VirtualBox Linux客户机挂载宿主机目录"
date: 2018-09-26T23:06:45+08:00
draft: false
layout: post
tags:
  - virtualbox
categories:
  - Linux
---

## 宿主机情况
OS：Ubuntu 18.04.1

## 前期准备
首先安装需要的编译组件
```bash
sudo apt install build-essential
```
<!--more-->
## 安装virtualbox增强功能

从虚拟机窗口点击‘设备’，安装增强功能，挂载至CDROM

![](https://raw.githubusercontent.com/istek/img/master/imgTIM-20180926220341.jpg)

挂载至CDROM后，之后从系统中挂载CDROM至mnt目录，安装`VBoxLinuxAdditions.run`
```bash
sudo mount /dev/cdrom /mnt
sudo /mnt/VBoxLinuxAdditions.run
```

安装完成后，重启客户机系统

```bash
sudo reboot
```

## 给客户机增加共享目录

选中客户机，点击`设置`

![](https://raw.githubusercontent.com/istek/img/master/imgTIM-20180926221337.jpg)

然后点击`共享文件夹`，添加共享文件夹

![](https://raw.githubusercontent.com/istek/img/master/imgTIM-20180926221519.jpg)

**注意：共享文件夹名称是挂载时需要使用的名称，请妥善命名**

## 手动挂载共享目录

使用`mount`命令挂载共享目录，文件类型是`vboxsf`，本例中共享目录名称为`temp`，所以命令如下：

```bash
mkdir /home/abc/temp
sudo mount -t vboxsf temp /home/abc/temp
```

## 自动挂载共享目录

在fstab中自动挂载总是失败，只能通过bashrc来挂载，即编辑.bashrc文件，增加挂载命令。


