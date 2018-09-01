---
title: 我的Ubuntu笔记
date: 2007-05-29T14:42:09+00:00
layout: post
categories:
  - 我的世界
tags:
  - Ubuntu
---
我好久好久没有用过LINUX了，很生疏，实在是很难受，当把系统装好后，都不知道QQ怎么装了，只记得Gaim有QQ插件，gaim现在已经改名为 Pidgin了。下面我把我的安装方法还有感受说一下，反正我看了很多guide，感觉大多数朋友的过程都不一样，这对于新手来说，可能容易犯糊涂。别担心，我写的这个安装办法适用很多很多朋友，那些不愿意动手的。PS：很多内容是靠记着的东西回忆起来的，可能有地方与你实际操作不一样，但是过程是一样的。
<!--more-->
一、安装

我觉得Ubuntu现在把安装做的越来越简单了，很多没有用过LINUX的朋友也能很轻松的安装Ubuntu了。

1.使用Ubuntu光盘引导系统，在菜单中选择第一项。

2.静静的等待UBUNTU加载成功，进入Ubuntu桌面。

3.桌面上应该有2个图标，其中一个是INSTALL，双击该图标，开始图形化安装。

4.1 国家和语言，选择简体中文。

4.2 硬盘分区。关于硬盘分区，我不多讲了，网络上到处都是指导文章，大家可以搜索看看。建议使用手工分区的办法。建议分3个驱，以40G硬盘为例，swap分区 512M，/分区 8G，/home分区 31.5G。

4.3 时区。默认是中国上海。不需要改变。

4.4 个人信息。填入你的昵称，这里也是你设置你的登录用户名的地方还有密码。注意：别把密码给忘记了。

4.5 开始安装。过程大约20-30分钟，如果你已经联网，会从网络上下载中文支持文件。

二、设置部分

1.安装成功后，去掉光盘，启动系统，进入Ubuntu登录界面。

2.输入你的用户名和密码登录系统。

3.点击应用程序（Application）-附件（Accessories）-终端（Terminal）,打开终端。

4.修改sources.list(软件源).
```
sudo cp /etc/apt/sources.list /etc/apt/sources.list_backup #备份sources.list
sudo gedit /etc/apt/sources.list #使用gedit程序编辑sources.list
```

按`Ctrl+A`全选，然后删除，加入下面的源，自己任选一组。

Ubuntu.cn99.com 更新服务器(江苏省常州市电信，推荐电信用户使用。）：
```
deb http://ubuntu.cn99.com/ubuntu/ feisty main restricted universe multiverse
deb http://ubuntu.cn99.com/ubuntu/ feisty-updates main restricted universe multiverse
deb http://ubuntu.cn99.com/ubuntu/ feisty-security main restricted universe multiverse
deb http://ubuntu.cn99.com/ubuntu/ feisty-backports main restricted universe multiverse
deb http://ubuntu.cn99.com/ubuntu/ feisty-proposed main multiverse restricted universe
deb http://ubuntu.cn99.com/ubuntu-cn/ feisty main restricted universe multiverse
```

Mirror.lupaworld.com 更新服务器(浙江省杭州市电信，推荐电信用户使用。）：
```
deb http://mirror.lupaworld.com/ubuntu/archive/ feisty main restricted universe multiverse
deb http://mirror.lupaworld.com/ubuntu/archive/ feisty-security main restricted universe multiverse
deb http://mirror.lupaworld.com/ubuntu/archive/ feisty-updates main restricted universe multiverse
deb http://mirror.lupaworld.com/ubuntu/archive/ feisty-backports main restricted universe multiverse
deb http://mirror.lupaworld.com/ubuntu/archive/ feisty-proposed main restricted universe multiverse
deb http://mirror.lupaworld.com/ubuntu/ubuntu-cn/ feisty main restricted universe multiverse
```

上海市交通大学 更新服务器（教育网，推荐校园网和网通用户使用。）：
```
deb http://ftp.sjtu.edu.cn/ubuntu/ feisty main multiverse restricted universe
deb http://ftp.sjtu.edu.cn/ubuntu/ feisty-backports main multiverse restricted universe
deb http://ftp.sjtu.edu.cn/ubuntu/ feisty-proposed main multiverse restricted universe
deb http://ftp.sjtu.edu.cn/ubuntu/ feisty-security main multiverse restricted universe
deb http://ftp.sjtu.edu.cn/ubuntu/ feisty-updates main multiverse restricted universe
deb http://ftp.sjtu.edu.cn/ubuntu-cn/ feisty main multiverse restricted universe
```

北京市清华大学 更新服务器（教育网，推荐校园网和网通用户使用。)：
```
deb http://mirror.net9.org/ubuntu/ feisty main multiverse restricted universe
deb http://mirror.net9.org/ubuntu/ feisty-backports main multiverse restricted universe
deb http://mirror.net9.org/ubuntu/ feisty-proposed main multiverse restricted universe
deb http://mirror.net9.org/ubuntu/ feisty-security main multiverse restricted universe
deb http://mirror.net9.org/ubuntu/ feisty-updates main multiverse restricted universe
deb http://mirror.net9.org/ubuntu-cn/ feisty main multiverse restricted universe
```

Mirror.vmmatrix.net 更新服务器（上海市电信，推荐电信网通用户使用。）:
```
deb http://mirror.vmmatrix.net/ubuntu/ feisty main restricted universe multiverse
deb-src http://mirror.vmmatrix.net/ubuntu/ feisty main restricted universe multiverse
deb http://mirror.vmmatrix.net/ubuntu/ feisty-updates main restricted universe multiverse
deb-src http://mirror.vmmatrix.net/ubuntu/ feisty-updates main restricted universe multiverse
deb http://mirror.vmmatrix.net/ubuntu/ feisty-backports main restricted universe multiverse
deb-src http://mirror.vmmatrix.net/ubuntu/ feisty-backports main restricted universe multiverse
deb http://mirror.vmmatrix.net/ubuntu/ feisty-security main restricted universe multiverse
deb-src http://mirror.vmmatrix.net/ubuntu/ feisty-security main restricted universe multiverse
deb http://ftp.sjtu.edu.cn/ubuntu-cn/ feisty main multiverse restricted universe
```

5.更新源与更新软件
```
sudo apt-get update #更新源
sudo apt-get dist-upgrade #更新已装软件
```

6.修改系统语言

点击 系统(System)-系统管理(Administration)-语言支持(Language Support),在打开窗口后，会下载需要的语言包，不用点取消，安装完成后，在窗口中支持语言选择Chinese和English，默认语言Chinese.接着会继续下载和安装语言支持的。

7.删除不需要的软件
```
sudo apt-get remove gaim gaim-data #稍后安装pidgin;
sudo apt-get remove scim #SCIM与很多软件冲突，建议安装FCITX
sudo apt-get install fcitx
sudo apt-get remove evolution #如果你不用Evolution的话，就删除它
```

*建议重启系统。

8.安装文泉译字体
```
sudo apt-get install wqy-bitmapfont
```

安装之后，应该可以从系统-首选项-字体中可以看到有WenQuanYi Bitmap Song。

9.安装Java支持
```
sudo apt-get install sun-java6-jdk
```

安装之后，设置为默认的Java解释器
```
sudo update-alternatives –config java
```

之后出现的画面中选择`2-/usr/lib/jvm/java-6-sun/jre/bin/java`

安装浏览器的Java Plugin
```
sudo apt-get install sun-java6-plugin
```

1. 安装tk8.4
```
sudo apt-get install tk8.4 #pidgin需要
```

1. 安装pidgin 2.0.1
```
sudo wget -c http://download.ubuntu.pl/_Feisty_Fawn/pidgin/2.0.1/pidgin_2.0.1-1_i386.deb
sudo dpkg -i pidgin_2.0.1-1_i386.deb
```

安装成功后，pidgin快捷方式在应用程序-Internet中打开。

12. 安装LumaQQ 2006
```
sudo wget -c http://lumaqq.linuxsir.org/download/2006/lumaqq_2006M2-linux_gtk2_x86_no_jre.tar.gz #32位版本LUMAQQ
sudo tar zxvf lumaqq_2006M2-linux_gtk2_x86_no_jre.tar.gz -C /opt/
sudo chown -R root:root /opt/LumaQQ/
sudo chmod -R 755 /opt/LumaQQ/
sudo gedit /usr/share/applications/LumaQQ.desktop
```

#将LumaQQ的快捷方式放入应用程序-Internet，文件内容如下:
```
[Desktop Entry]
Name=LumaQQ
Comment=QQ Client
Exec=/opt/LumaQQ/lumaqq
Icon=/opt/LumaQQ/QQ.png
Terminal=false
Type=Application
Categories=Application;Network;
```

13.安装ThunderBird邮件新闻组程序
```
sudo apt-get install mozilla-thunderbird
```

14.安装多媒体播放器
```
sudo apt-get install audacious audacious-plugins audacious-plugins-extra libdvdcss2 libdvdnav4 libdvdplay0 libdvdread3 w32codecs libxine-extracodecs gstreamer0.10-pitfdll gstreamer0.10-ffmpeg gstreamer0.10-plugins-bad gstreamer0.10-plugins-bad-multiverse gstreamer0.10-plugins-ugly gstreamer0.10-plugins-ugly-multiverse
```

安装完成后，已经可以播放RMVB，WMA,asf等等类型的媒体文件了。音乐播放器是audacious，电影播放器是totem。它们的快捷方式都在应用程序-影音中。

15.安装RealPlayer

首先，打开`http://forms.helixcommunity.org/helix/builds/?category=realplay-current`

下载最新版本的BIN文件
```
sudo chmod 755 xxxxxxxxxxxx.bin
sudo ./xxxxxxxxxxxx.bin
```

根据提示安装，安装完成后，就可以使用了。快捷方式在应用程序-影音-RealPlayer。

16.字体设置

系统 -首选项 – 字体：

设置所有非等宽字体为WenQuanYi Bitmap Song 10号，其中窗口标题字体设置为粗体。等宽字体设置为`Courier 10 Pitch`，因为Courier 10 Pitch比Courier New字体更清楚。为了避免英文字体变得毛躁，所以还是保持英文字体的AA是打开的，所以字体渲染部分设置保持默认。

Firefox中，可以这样设置：

语言编码：简体中文

比例字体：无衬线字体 大小：16

衬线字体：Times New Roman(若没有该字体，可选择WenQuanYi Bitmap Song)

无衬线字体：WenQuanYi Bitmap Song

等宽字体：Courier 10 Pitch 大小：16

屏幕解析度：96dpi 最小字体：11

（选中）允许页面选择显示字体而无需使用上面的配置

默认字符编码：UTF-8

17.安装rar解压软件
```
sudo apt-get install unrar rar
```

18.右键菜单增加”打开终端”快捷方式
```
sudo apt-get install nautilus-open-terminal
```

19.安装VIM-FULL
```
sudo apt-get install vim-full
```

20.安装完成，重启电脑。

装好后，系统的程序列表

媒体播放：totem(系统自带)+realplay+audacious

网络聊天：pidgin+lumaqq

浏览器：firefox（系统自带）

邮件新闻组程序：Thunderbird

中文输入法:fcitx

办公软件：openoffice（系统自带）

一般使用上，是没有问题了，如果需要BT，或者amule的话，就`apt-cache search xxx`，搜索一下，或者使用新立德软件包管理器搜索。我装好后，可以收看本地netlife在线影院的电影，听音乐，聊天，办公，处理图片等等。

三、常见问题

1.Flash在Firefox中无法看到？

答：当打开含有FLASH的网页时，Firefox会提示有缺失插件，点击消息条安装Flash插件。或者使用命令`sudo apt-get install flashplugin-nonfree`

2.无法在Firefox中打开在线音频或者视频？

答：访问这个地址`http://addons.mozine.cn/firefox/116/`安装MediaWrap插件，重启Firefox就可以用了。

3.如何修改用户root的密码？

答： `sudo passwd root`。

4.如何让Linux支持NTFS？

答：`sudo apt-get install ntfs-config`，点击“应用程序” – “系统工具” – “NTFS写入支持配置程序”，输入密码，选中对内部设备的读写支持，和外部设备的读写支持，系统会自动扫描你硬盘上的NTFS分区，并重新挂载，这样，你的NTFS分区就能在Feisty下完美读写了！

5.如何在LINUX打开局域网内其他WINDOWS机器的硬盘？

答：点击”位置”-“连接到服务器”-在弹出窗口中，服务类型选择“windows 共享”，服务器栏填写IP地址，用户名填写WINDOWS机器上的用户名，然后单击连接。

6.为什么在播放RMVB文件的时候，没有声音？

答：首先关闭播放器，然後使用你喜欢的文本编辑器，比如gedit，vim等打开文件 `~/.xine/catalog.cache`，（比如 gedit `~/.xine/catalog.cache`）打开该文件。找到其中的
```
/usr/lib/xine/plugins/1.1.4/xineplug_decode_real_audio.so
```

代码段，将其下的 `decoder_priority` 的数值修改成 `10`

修改完毕後这一段应该看起来是这样子的
```
/usr/lib/xine/plugins/1.1.4/xineplug_decode_real_audio.so
….
decoder_priority=10
```

7.如何更改登录窗口？

答：打开`http://www.gnome-look.org`,点击Content栏目内的GDM Themes，下载一个自己喜欢的GDM主题，然后打开系统-系统管理-登录窗口，点击本地标签，将你下载的tar.bz2文件拖入主题栏目，安装主题，然后选择上刚刚安装的主题，点击关闭。

8.如何更改系统主题?

答：方法如上。系统主题应该是gtk 2.0,Metacity.然后打开系统-首选项-主题，将下载的文件拖入主题列表安装，安装完整后，应用新主题。

9.Firefox调用rtsp协议时，totem吊死了，怎么办？

答：目前我也没有找到什么好办法，只能使用realplay来处理rtsp方式的连接。修改方法：在终端输入`gconf-editor`，打开配置编辑器，浏览节点`/desktop/gnome/url-handlers/rtsp`,将名称`command`的值改为`realplay "%s"`.

10.如何更改登录后加载系统时候的slashscreen？

答：首先，你需要找一张png图片，然后安装gnome-splashsreen-manager`sudo apt-get install gnome-splashscreen-manager`,系统-首选项-Splash Screen，点击INSTALL，选择你的PNG图形，然后选中你的PNG图形，点击Active（激活）。如果你不想用了，直接Delete删除。

11.如何更改Grub Usplash界面？

答：首先，到`http://web.telia.com/~u88005282/sum/archive/deb/`这个站点下载最新的`start-up manager`，然后安装后就可以使用了。系统-系统管理-StartUp Manager。具体的安装细节可以到[这个站点](http://web.telia.com/~u88005282/sum/installation.html)阅读。

目前好像没有其他的了。我会陆续添加的，只要大家有问题。很多东西是来自`wiki.ubuntu.com.cn`。谢谢各位 linux粉丝的贡献。