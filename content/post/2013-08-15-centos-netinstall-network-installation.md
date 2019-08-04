---
title: 网络安装CentOS 5.9
date: 2013-08-15T23:20:38+00:00
layout: post
categories:
  - Linux
tags:
  - netinstall
---

## 1. 下载网络安装镜像
**选择这里的镜像:**

* [i386 version](http://mirrors.sohu.com/centos/5.9/isos/i386/CentOS-5.9-i386-netinstall.iso)
* [x86_64 version](http://mirrors.sohu.com/centos/5.9/isos/x86_64/)

**下载ISO镜像文件**

* CentOS-5.9-i386-netinstall.iso
* CentOS-5.9-x86_64-netinstall.iso

## 2. 刻录镜像文件与使用CentOS安装CD引导计算机

检查CentOS镜像的MD5，之后使用你喜欢的工具刻录至光盘。最后，使用CentOS安装光盘引导计算机。 
<!--more-->
## 3. 在GRUB菜单中选择网络安装 (NetInstall)或直接按回车键开始引导系统

![Centos 5.9 Netinstall boot](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002845/35b240bfa6c894775e882f9c380bd853_gxzd05.png "Centos 5.9 Netinstall boot")

## 4. 启动CentOS 5.9安装工具

![Booting CentOS 5.9 Installer](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002844/e840bdb0d402680e8e2edaf9ab3f1b00_rwlchb.png "Booting CentOS 5.9 Installer")

## 5. 选择语言（Chinese Simplified简体中文或者English英语）

![Choose a Language](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002844/4bf5f0077cbaf7c809ba71c8bdc4d6ca_vw3o4j.png "Choose a Language")

## 6. 选择键盘类型（保持默认即可）

![Select keyboard type](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002843/6ccb70516bc95fbde240038f55006575_gm15qf.png "Select keyboard type")

## 7. 选择安装方式（这里我们选择HTTP）

![Select Network Installation Method](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002771/505d663465a1107c4a90cb1948be4dce_r4zeks.png "Select Network Installation Method")

## 8. 配置TCP/IP设置

（勾选Enable IPv4 support,如果没有IPv6网络，可不勾选Enable IPv6 Support）

![Configure TCP/IP settings](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002772/101121c9043cae25a95438ef09575737_eiss8o.png "Configure TCP/IP settings")

## 9. 设置网络安装CentOS 5.9的URL (软件源站点和CentOS目录)

**Web site name:**

> mirror.sohu.com

**CentOS directory for i386:**

`/centos/5.9/os/i386/` or `/centos/5/os/i386`

**CentOS directory for x86_64:**

`/centos/5/os/x86_64` or `/centos/5.9/os/x86_64`

![CentOS 5 Netinstall url-http setup](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002773/1859f06c394f5559aa78752870bed1a6_c44qwh.png)

## 10. 开始下载安装工具

![Downloading Installer](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002841/b68f4296cf3b67447c0bca133c36a3b0_l1a7zs.png "Downloading Installer")

## 11. CentOS安装欢迎屏幕

![CentOS 5.9 Welcome to CentOS](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002842/2491b8027f3edd12e82686cb66e23078_ggqkiy.png "CentOS 5.9 Welcome to CentOS")

## 12. 进行分区操作

若打算修改分区大小，文件系统等选项，请选择“Create custom layout”选项.

![CentOS 5.9 Select partitioning type](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002770/43b823b86d55ffe7c4c43216fb456795_lctrqm.png "CentOS 5.9 Select partitioning type")

现在就可以开始修改分区了.

![CentOS 5.9 Partition layout](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002770/2ece2c5f1ed69faad1b80ac58db58d43_srybbs.png "CentOS 5.9 Partition layout")

## 13. 配置启动引导工具

![CentOS 5.9 Bootloader setup](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002769/cac1ecc898e80df2736c2c9c2e0ea0e3_uq4ixe.png "CentOS 5.9 Bootloader setup")

## 14. 配置网络

![CentOS 5.9 Network setup](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002768/d110f9d249901362a66d01675207701b_mitwf5.png "CentOS 5.9 Network setup")

## 15. 选择时区

![CentOS 5.9 Timezone setup](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002767/aee41d88f6ed82d0424b4d9e3ab7401a_yezpmj.png "CentOS 5.9 Timezone setup")

## 16. 设置root密码

![CentOS 5.9 Set root password](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002763/b88abd80220d82cc23eae31ad0ccec70_o7dagr.png "CentOS 5.9 Set root password")

## 17. 选择需要安装的软件包

![CentOS 5.9 Select installation type](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002764/cf11eaefa2b2fd9d200a2261085a6fea_vikizz.png "CentOS 5.9 Select installation type")

## 18. 开始安装已选择的软件包

![CentOS 5.9 Installing selected packages](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002765/4a39de7529b95cdd8399ab6ff102dfcc_xdyn90.png "CentOS 5.9 Installing selected packages")

## 19. 安装结束，重启

![CentOS 5.9 Installation complete](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002766/3bac40f27fe3f2fd7d4b85e8490de35d_te3jqs.png "CentOS 5.9 Installation complete")

## 20. 系统成功启动至命令提示符

![CentOS 5.9 Shell Login](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1413002762/d398475a36340fc856074fe1d565cca6_ecyviy.png)