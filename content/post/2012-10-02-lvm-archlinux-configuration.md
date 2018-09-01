---
title: LVM下的archlinux安装
date: 2012-10-02T21:22:17+00:00
layout: post
categories:
  - 我的世界
tags:
  - arch
---

昨天晚上在自己的笔记本上用LVM，尝试一下linux使用lvm的效果，结果grub的安装上就存在问题，安装不了。grub2.0是支持全盘lvm的，但是我不知道为什么，总是安装不成功。目前查阅了一些文章，两种方案:

* /boot分区单独分出来。
* 如果全部是lvm的话，使用lilo。

即便这样，还是会出现同样的问题。启动的时候，会出现一个错误信息

`no volume groups found`（没有找到卷组），然后启动就停止了。很显然，卷组没有被激活，挂载不了。

这时候，必须要`modprobe dm-mod`，然后`lvm vgchange -ay`,然后再退出exit，启动就能进行下来。很奇怪的问题。按照官方文档的方法，已经在`rc.conf`中`USELVM=yes`，`mkinitcpio.conf`的hooks里面添加了LVM2，但是依旧存在问题。

我看arch论坛的人修改hooks脚本，来达到使lvm卷组被激活。
<!--more-->
`/usr/lib/initcpio/hooks/lvm2`
```
    #!/usr/bin/ash

    run_hook() {
		local pvdev modprobe -q dm-mod >/dev/null 2>&1
		# If the lvmwait= parameter has been specified on the command line
		# wait for the device(s) before trying to activate the volume group(s)
		for pvdev in ${lvmwait//,/ };
		do poll_device ${pvdev} ${rootdelay} done
			msg "Activating logical volumes..."
			lvm pvscan [ -d /etc/lvm ] && lvm vgscan
		if [ -n "$quiet" ];
			then lvm vgchange --sysinit -a y >/dev/null
			else lvm vgchange --sysinit -a y
		fi
	}
```

修改后，系统出现kernel panic？！lvmwait。。。LILO？！加在哪里？？？

