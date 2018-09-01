---
title: Wubi安装Lubuntu
date: 2011-12-27T19:39:22+00:00
layout: post
categories:
  - 我的世界
tags:
  - Ubuntu
---

想使用Ubuntu的Windows用户，又不想重新分区安装的，不可或缺的安装工具就是Wubi了。Wubi在下载的系统镜像中就有，这里要说的是要用它安装Lubuntu，因为在该工具的可安装的系统的选择项中并没有Lubuntu。

关于Lubuntu呢？其实就是使用LXDE桌面环境的Ubuntu。而LXDE呢？是由台湾的开发者开发的轻量级Linux桌面环境，据称对内存的需求只有128M，对于现代的电脑来说，太小菜了。LXDE除了轻量之外，也保证了一定的美观，对于需要轻量又不失美观的用户，可以考虑使用这款系统。当然，Lubuntu也时常被推荐为安装在老旧的机器上，让老旧的机器也能焕然新生。其实，LinuxKernel一直以来对硬件就没有怎么挑剔过，至于桌面环境嘛，毕竟它也是一个较为庞大而复杂的软件套装。

言归正传，使用Wubi安装Lubuntu。其实很简单，只是你需要准备两个系统镜像文件，一个是Ubuntu的iso镜像，一个是Lubuntu的iso镜像。首先，运行任意镜像中的Wubi（你可以安装winmount，使用它将镜像挂在到一个盘符中，或者使用其他的虚拟光驱软件），选择安装Ubuntu（另外还有Kubuntu和Xubuntu，因为我们的镜像是Ubuntu的，所以选择Ubuntu），安装。安装好后，不要重启，先将Lubuntu的镜像拷贝到Ubuntu的安装目录中的install目录下，删除installation.iso，并将Lubuntu的镜像更名为installation.iso。接着，重启安装Ubuntu。
<!--more-->
安装好后，像Ubuntu一样，如果语言环境安装不完整，系统会提示你安装。如果输入法没有安装，可以安装ibus-pinyin，安装完成后，需要注销一下系统。其他的如音视频编解码器、rar解压之类的，可以安装lubuntu-restricted-extras。