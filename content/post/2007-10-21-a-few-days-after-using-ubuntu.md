---
title: 使用了几天Ubuntu之后
date: 2007-10-21T07:06:30+00:00
layout: post
categories:
  - Linux
---

7月18日，一个非常特殊的日子，对于所有使用[Ubuntu](http://www.ubuntu.org.cn)发行版的朋友来说，因为Ubuntu在这一天全球发布了Ubuntu 7.10(开发代号Gutsy Gibbon)。虽然我也使用Ubuntu，但是从来没有碰到过这样特殊的日子，18号一天都在电脑前等着呢，说实话，从来没有这么期盼过一个操作系统的诞生，在Ubuntu中文论坛有朋友说，Windows Vista发布也没有这么热闹过。很多朋友耐心的等待，终于在18号晚上Ubuntu官方网站的首页改版，放出了Release。

使用了几天之后，我觉得可能我有些月没有用过了，还是对命令和文件系统生疏了许多。在这一版里，Ubuntu默认启用了NTFS文件系统的读写支持以及打开了Compiz Fusion桌面效果，当时我看到后就一个感想，太酷了，效果真的棒的没法说，当然了，前提条件是显卡要支持，我的电脑配置有点过时了，内存1G，显卡是Nvdia Geforce2 MX400，支持没有问题，速度不慢.

本着Linux自由、分享的精神，下面我把我遇到的一些问题及解决办法贴出来，相信有很多朋友也遇到过。呵呵。</span>

<!--more-->
1.关于源的问题。

我个人使用tw的源。具体地址如下:

```
deb http://tw.archive.ubuntu.com/ubuntu gutsy main restricted universe multiverse
deb http://tw.archive.ubuntu.com/ubuntu gutsy-security main restricted universe multiverse
deb http://tw.archive.ubuntu.com/ubuntu gutsy-updates main restricted universe multiverse
deb http://tw.archive.ubuntu.com/ubuntu gutsy-backports main restricted universe multiverse
deb http://tw.archive.ubuntu.com/ubuntu gutsy-proposed main restricted universe multiverse
deb-src http://tw.archive.ubuntu.com/ubuntu gutsy main restricted universe multiverse
deb-src http://tw.archive.ubuntu.com/ubuntu gutsy-security main restricted universe multiverse
deb-src http://tw.archive.ubuntu.com/ubuntu gutsy-updates main restricted universe multiverse
deb-src http://tw.archive.ubuntu.com/ubuntu gutsy-backports main restricted universe multiverse
deb-src http://tw.archive.ubuntu.com/ubuntu gutsy-proposed main restricted universe multiverse
```

2.安装系统后，窗口标题栏没有了！

解决办法有三个:

- 先备份原来的配置文件
```
cp /etc/X11/xorg.conf /etc/X11/xorg.conf.bak
```

然后在xorg.conf中Module段加入
```
SubSection "extmod"
Option "omit xfree86-dga"
EndSubSection
```

在Device段中加入
```
Option "DisableGLXRootClipping" "True"
Option "AddARGBGLXVisuals" "True"
Option "AllowGLXWithComposite" "True"
Option "RenderAccel" "True"
```

在配置文件的最后加入
```
Section "Extensions"
Option "Composite" "Enable"
EndSection
```

- 如果试N卡的话, 运行下面的命令, 再重启X.

```
sudo nvidia-xconfig --add-argb-glx-visuals #解决没有窗口边框的问题
```

- `sudo dpkg-reconfigure -phigh xserver-xorg`

```
OPTION ADDRGBGLXVirtual "true"
```


2.雅黑字体的安装。

默认的字体效果其实可以，但是为了有Windows Vista的界面字体效果，可以使用下面的办法。

```
#创建文件夹yahei
sudo mkdir /usr/share/fonts/yahei
#将微软雅黑（MSYH.ttf&MSYHBD.ttf）字体放入/usr/share/fonts/yahei文件夹
sudo cp /home/xxxx/*.ttf /usr/share/fonts/yahei
sudo chmod +rx  /usr/share/fonts/yahei/*
#编辑/etc/X11/xorg.conf
sudo gedit /etc/X11/xorg.conf
#加入一行
FontPath "/usr/share/fonts/yahei"
#示例
#-----------------------------------------------------------------
Section "Files"
RgbPath "/usr/lib/X11/rgb"
FontPath "/usr/share/fonts/vista"
EndSection
#----------------------------------------------------------------- #
建立字体缓存信息
fc-cache -fv
#修改/etc/fonts/language-selector.conf
sudo gedit /etc/fonts/language-selector.conf
#分别在(不含"!"号)
!<family>Bitstream Vera Serif</family>
!<family>Bitstream Vera Sans</family>
!<family>Bitstream Vera Sans Mono</family>
#此为默认字体,如果更改过字体,<family>标签中的即为你更改过的字体 #下一行添加 <family>Microsoft YaHei</family>
#示例
#-----------------------------------------------------------------
<alias>
<family>serif</family>
<family>Microsoft YaHei</family>
<prefer>
<family>Bitstream Vera Serif</family>
<family>Microsoft YaHei</family>
<family>DejaVu Serif</family>
<family>AR PL ShanHeiSun Uni</family>
<family>WenQuanYi Bitmap Song</family>
<family>AR PL ZenKai Uni</family>
</prefer>
</alias>
<alias>
<family>sans-serif</family>
<family>Microsoft YaHei</family>
<prefer> <family>Bitstream Vera Sans</family> <family>Microsoft YaHei</family>
<family>DejaVu Sans</family>
<family>AR PL ShanHeiSun Uni</family>
<family>WenQuanYi Bitmap Song</family>
<family>AR PL ZenKai Uni</family>
</prefer>
</alias>
<alias>
<family>monospace</family>
<family>Microsoft YaHei</family>
<prefer>
<family>Bitstream Vera Sans Mono</family>
<family>Microsoft YaHei</family>
<family>DejaVu Sans Mono</family>
<family>AR PL ShanHeiSun Uni</family>
<family>WenQuanYi Bitmap Song</family>
<family>AR PL ZenKai Uni</family>
</prefer>
</alias>
#------------------------------------------------------------------------
```

然后在系统->首选项->字体中选择微软雅黑即可

3.关于w32codecs和libdvdcss2的问题。

毫无疑问，w32codecs是播放wmv,wma,rmvb等等文件不可缺少的解码包，而libdvdcss2似乎源里没有。现在把这两个包的安装办法记录下来。
```
sudo gedit /etc/apt/sources.list
```

加入
```
deb http://medibuntu.sos-sts.com/repo/ feisty free non-free
deb-src http://medibuntu.sos-sts.com/repo/ feisty free non-free
```

加入key
```
wget -q http://packages.medibuntu.org/medibuntu-key.gpg -O- | sudo apt-key add -
```

更新

```
sudo apt-get update
```

安装
```
sudo apt-get install w32codecs libdvdcss2
```

4.什么播放器比较适合自己？

现在播放器有那么多，到底哪个适合自己呢？答案是肯定的，我列出我的选择，smplayer+audacious这两个就可以很好的处理视频和音频文件了，smplayer类似于win下面的暴风影音工具。安装步骤我再不叙述，安装samplayer之前应该安装mplayer，然后就是安装audacious了。smplayer点击<a href="http://smplayer.sourceforge.net/">这个网站</a>下载，其他的源里都有。

5.scim输入法与很多软件冲突，有没有办法解决呢？

scim输入法是ubuntu下的一种输入软件，相似的还有小企鹅输入法，因为目前小企鹅输入法已经暂停维护了，所以还是使用scim吧，即便有不兼容的情况发生。
```
im-switch -s scim -z default
sudo apt-get install scim-qtimm
sudo apt-get install scim scim-pinyin scim-tables-zh im-switch scim-qtimm scim-bridge scim-bridge-client-gtk scim-bridge-client-qt scim-bridge-agent
```

编辑im-switch生成的scim配置文件

```
# gksu gedit /etc/X11/xinit/xinput.d/scim
```

将默认的`GTK_IM_MODULE=scim`修改为`GTK_IM_MODULE="scim-bridge"`。保存退出. 在scim输入法中进行了如下设定： scim设置－>全局设置－>将预编辑字符串嵌入到客户端中 前的勾去掉 scim设置->gtk->嵌入式候选词标的勾去掉.

重启scim

打开终端,输入`pkill scim`然后输入`scim -d`

6.GnuPG默认安装了，但是是字符界面的命令，是否有图形界面的程序呢？

有！是seahorse！可以从源里安装。

7.如何修改菜单中的菜单或者某个程序的图标？

系统－首选项－主菜单。点击某个应用程序的属性来修改。

8.IPv6的关闭。

Ubuntu默认是打开IPv6协议的。关闭步骤如下：
```
sudo gedit /etc/modprobe.d/aliases
```

将`alias net-pf-10 ipv6`改为`alias net-pf-10 off`
```
sudo gedit /etc/sysctl.conf
```

加入
```
net.ipv4.tcp_ecn = 0
```

打开firefox, about:config，将ipv6禁用。

9.所有的菜单文件的位置。

```
/usr/share/applications/
```

10.一些好用的软件。

eva 类似QQ；sylpheed轻量级的邮件客户端，也支持新闻组；streamtuner在线广播软件；adobe reader不用我说了吧，pdf真是好东西！

未完待续。

附上一张我的桌面图吧，呵呵，没有加什么主题。本文感谢Cash给予极大的帮助！
