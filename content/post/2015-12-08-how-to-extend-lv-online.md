---
title: Linux下在线扩LV
date: 2015-12-08T06:14:55+00:00
layout: post
categories:
  - 我的世界
tags:
  - RHEL
  - 使用技巧
---
以前从来没有在Linux下面扩过LV，LVM逻辑卷管理很多都是使用在Unix类系统的，如HP-UX,AIX此类的，之后，在kernel 2.4版本实现的。扩lv的一般步骤其实大同小异，建PV，扩卷组，扩LV，伸缩文件系统，在HP-UX下，必须是离线操作的，也就是umount挂载点，然后扩LV的。

在Linux下就方便了很多，可以直接在线操作。OK，下面是操作流程：

<!--more-->

# 扩展

## 1、创建物理卷

我们的管理员给我的服务器新建了一个分区，是`/dev/sda5`，分区大小30G。
```
# pvcreate /dev/sda5
```

之后使用pvdisplay检查新增的物理卷。
```
# pvdisplay –v
```

![1](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449669738/120715_0628_LinuxLV1_yd8szw.png)

## 2、扩展卷组

现在我们需要将这个PV增加到卷组。
```
# vgextend datavg /dev/sda5
```

![2](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449669737/120715_0628_LinuxLV2_o9anli.png)

扩展后，我们可以通过`vgdisplay`查看卷组的`free pe/Size`来确定有可用于扩展的空间。

![3](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449669737/120715_0628_LinuxLV3_grqrte.png)

## 3、扩展LV

现在VG里面已经有空间了，可以用来扩我们需要扩的LV。
```
# lvextend –L +29G /dev/mapper/datavg-datalv01
```

![4](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449669736/120715_0628_LinuxLV4_mc4iaj.png)

## 4、扩展文件系统

LV扩完后，还需要扩展文件系统。
```
# resize2fs /dev/mapper/datavg-datalv01
```

![5](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449669735/120715_0628_LinuxLV5_ccpzvh.png)

## 5、检查扩展后的LV情况
```
# lvdisplay –v
```

![6](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449669733/120715_0628_LinuxLV6_xnlflk.png)

## 附:收缩

先减文件系统再减LV（注意顺序）
```
umount /dev/vg1/lv1
e2fsck -f /dev/vg1/lv1
resize2fs /dev/vg1/lv1 100M 减小文件系统到100M
lvreduce -L 100M /dev/vg1/lv1 减小逻辑卷到100M
mount -a
```

