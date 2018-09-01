---
title: V2ray，一个值得信赖的新工具
date: 2017-11-05T10:01:38+00:00
layout: post
categories:
  - 我的世界
tags:
  - v2ray
---

# 多协议支持

[V2Ray](https://www.v2ray.com/) 原生支持 Socks、HTTP、Shadowsocks、VMess 等协议。

* 在一个进程中可以配置不同的端口使用不同的协议进行通讯。
* 通过不同的传入和传出协议组合，灵活转换通讯格式。

# 可变传输载体

上述协议均可使用 TLS、TCP、mKCP 等传输方式进行传输。

# 灵活的路由

通过内置的路由功能，V2Ray 可以灵活地实现选择性转发、直连或是阻止部分连接。

# 多平台支持

V2Ray 在以下平台中可用：

* Windows Vista 及之后版本（x86 / amd64）；
* Mac OS X 10.7 及之后版本（amd64）；
* Linux 2.6.23 及之后版本（x86 / amd64 / arm / arm64 / mips64 / mips）；
  * 包括但不限于 Debian 7 / 8、Ubuntu 12.04 / 14.04 及后续版本、CentOS 6 / 7、Arch Linux；
* FreeBSD (x86 / amd64)；
* OpenBSD (x86 / amd64)；

<!--more-->

# [V2ray.fun](https://github.com/FunctionClub/v2ray.fun)

V2ray控制脚本，向导式更改端口，加密方式，传输协议，享受V2ray的乐趣~

**注意：此脚本已经删除。**

## 功能

* 一键 启动 / 停止 / 重启 V2ray 服务端
* 自动随机生成 UUID
* 自助修改端口
* 快速查看服务器连接信息
* 一键下载客户端配置文件（**仅限 Xshell**）
* 自由更改**传输配置**：
  * 常规TCP
  * HTTP头部伪装
  * WebSocket流量
  * 常规mKCP流量
  * mKCP 伪装 FaceTime通话流量
  * mKCP 伪装 BT下载流量
  * mKCP 伪装 微信视频通话流量

**WebSocket不包括Nginx分流，请自行安装Nginx来分流。**

![1](https://github.com/FunctionClub/v2ray.fun/raw/master/1.png)

使用起来非常方便，对于不会配置文件的小白来说，太方便了。
