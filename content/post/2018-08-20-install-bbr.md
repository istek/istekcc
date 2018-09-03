---
title: "安装 Google BBR 加速VPS网络"
date: 2018-08-20 12:22:00
layout: post
tags: 
  - bbr
categories:
  - 分享
---

在使用Google BBR之前，我们首先要了解它是什么。了解计算机网络的人都知道，在TCP连接中，由于需要维持连接的可靠性，引入了拥塞控制和流量管理的方法。Google BBR就是谷歌公司提出的一个开源TCP拥塞控制的算法。

由于Google BBR非常新，任何低于4.9的linux内核版本都需要升级到4.9及以上才能使用，故若VPS本身内核版本较低的话，只有KVM架构的VPS才能使用本教程升级内核并使用，openvz的VPS用户若内核版本较低则无法使用！

<!--more-->

1.使用root用户登录，运行以下命令：
2.下载脚本，执行

```bash
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh
chmod +x bbr.sh
./bbr.sh
```

3.安装完成后，脚本会提示需要重启 VPS，输入 y 并回车后重启。 
重启完成后，进入 VPS，验证一下是否成功安装最新内核并开启 TCP BBR，输入以下命令：

4.`sysctl net.ipv4.tcp_available_congestion_control` 返回 `bbr cubic reno`
