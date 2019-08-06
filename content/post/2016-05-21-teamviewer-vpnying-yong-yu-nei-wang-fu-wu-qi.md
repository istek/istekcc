---
title: TeamViewer VPN应用于内网服务器
date: 2016-05-21T06:28:26+00:00
layout: post
categories:
  - Windows
tags:
  - teamviewer
---

有时候我们会遇到如下的情况：

* 尽管你有大屏幕，在TeamViewer中操作远程机器显示憋屈;
* 多人抢着用TeamViewer；
* 上传下载很麻烦;

那么现在我们可以通过TeamViewer的VPN和代理软件，实现本地机器无障碍在本地访问远程内网。

这里的TeamViewer的VPN，如果你使用TV连接后，仅仅是本地机器与远程机器连为一个网站，但是不能访问远程机器的内网资源。

<!--more-->

现在我们就来实际操作一下，假定有一台笔记本A，内网机器B，B机能访问内网的数据库，内网WEB应用及FTP内网其他机器。

## 一、TeamViewer

我们在A和B都安装TeamViewer 11,注意一个细节，安装的时候要选择**VPN**:

![1](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1463702781/QQ%E5%9B%BE%E7%89%8720160520075745_mkfadk.png)

![2](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1463702674/QQ%E6%88%AA%E5%9B%BE20160520075845_ag4puy.png)

A机和B机都安装完成后，就可以从A机VPN连接过去了。在伙伴ID处输入B机的ID，然后选择VPN，点击开始连接，提示输入B机的连接密码。

![3](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1463703009/QQ%E6%88%AA%E5%9B%BE20160520080948_mwthdv.png)

连接成功后，会有一个VPN连接状态窗口，显示A机和B机的IP地址以及流量情况。

![4](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1463703141/QQ%E6%88%AA%E5%9B%BE20160520081200_glvi2n.png)

可以点击“测试ping”，看看是否能和B机连通，如果出现超时的情况，请关闭B机的防火墙。

现在，A机和B机已经联为一个网络了，可以通过Explorer浏览、复制和剪贴B机的文件，接下来就是重点，让A机可以访问B机的内网资源。

## 二、CCPROXY

CCProxy是国内最流行的下载量最大的的国产代理服务器软件。主要用于局域网内共享宽带上网，ADSL共享上网、专线代理共享、ISDN代理共享、卫星代理共享、蓝牙代理共享和二级代理等共享代理上网。

下载[CCPROXY 8.0](http://pan.baidu.com/s/1mipO5AS)，安装在B机上，安装过程不再敖述，设置界面保持默认即可。

![5](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1463703672/QQ%E6%88%AA%E5%9B%BE20160520081640_rptghb.png)

## 三、Proxifier

Proxifier 是一款功能非常强大的socks5客户端，可以让不支持通过代理服务器工作的网络程序能通过HTTPS或SOCKS代理或代理链。支持64位系统，支持XP，Vista，Win7，支持socks4，socks5，http代理协议，支持TCP，UDP协议，可以指定端口，指定IP，指定程序等运行模式，兼容性非常好。

有许多网络应用程序不支持通过代理服务器工作，因此不能用于局域网或防火墙后面。这些会损害公司的隐私和导致很多限制。Proxifier 解决了这些问题和所有限制，让您有机会不受任何限制使用你喜爱的软件。此外，它让你获得了额外的网络安全控制，创建代理隧道，并添加使用更多网络功能的权力。

Proxifier 使您可以：

1. 通过代理服务器运行任何网络应用程序。对于软件不需要有什么特殊配置;整个过程是完全透明的。
2. 通过代理服务器网关访问受限制的网络。
3. 绕过防火墙的限制。

下载[Proxifier](http://pan.baidu.com/s/1i4MO0xZ)，安装到A机，安装过程不再敖述。

安装完成之后，运行软件，点击`配置文件`->`代理服务器`，打开如下窗口

![6](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1463705554/QQ%E6%88%AA%E5%9B%BE20160520084912_emk2zb.png)

点击`添加`，弹出添加代理服务器对话窗口，填入B机的TV IP(伙伴IP)和代理服务器端口，如下图设置：

![7](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1463705563/QQ%E6%88%AA%E5%9B%BE20160520084934_uqixfy.png)

点击确定，之后Proxifier就开始工作了。

## 三、测试

我们测试从A机访问B机所在内网的oracle数据库，使用dbvisualizar，不用做任何设置，直接连接：

![8](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1463705575/QQ%E6%88%AA%E5%9B%BE20160520085145_vaxqrj.png)

可以看到，数据库很快就连接上了，现在可以无障碍的访问内网资源了。

## 四、其他注意事项

因为A机是连接B机的代理服务器，访问B机所在的内网，所以A机的所有网络访问，不论是内网，还是公网，都走得B机的代理，比如A机的QQ，网页等使用网络的客户端。

我曾尝试，在连接TV的VPN之后，在B机新建VPN连接，然后A机使用伙伴IP远程连接B机的VPN，相当于访问了两次B机，期望达到拨号VPN后，获取B机DHCP制定的IP，以便访问B机的内网资源，但是失败了，如果您有更好的办法，也希望您能指点我一二。

