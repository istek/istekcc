---
title: "原生安卓解决感叹号"
date: 2019-01-06T12:47:45+08:00
draft: false
layout: post
tags: 
  - andriod
categories:
  - andriod
---

目前互联网上已经有了很多 Captive Portal Server ，如果你已经在使用一个稳定的，那么你是没有必要更改的。我们提供的这个地址只是希望能够让大家可以有多一种选择而已。

`captive.v2ex.co`

你可以使用 adb[^adb] 命令行工具这样设置：

```
adb shell "settings put global captive_portal_server captive.v2ex.co"
```

如果使用的是 OS X ，那么你可以通过 Homebrew 获取 adb ：

```
brew install android-platform-tools
```
<!--more-->
如果使用使用的是 Windows ，那么可以使用下面这个帖子里提到的快速安装 adb 的工具：

[下载工具](https://dl.google.com/android/repository/platform-tools-latest-windows.zip)

如果你使用的是 Ubuntu/Debian ，那么你可以在添加一个 PPA 包之后，通过 apt-get 获得 adb ：

```
sudo add-apt-repository ppa:phablet-team/tools && sudo apt-get update
sudo apt-get install android-tools-adb android-tools-fastboot
```

如果你已经完整安装了 Android Studio 的最新版本，那么 adb 命令行工具也可以在 sdk/platform-tools 目录下找到。

你可以把下面的这个地址加入到 $PATH 中：

```
~/Library/Android/sdk/platform-tools
```

如果你最近更新到了 Android 7.1.1，那么这个设置的语法稍有改变，请使用下面的指令重新设置：

```
adb shell "settings put global captive_portal_https_url https://captive.v2ex.co/generate_204"; 
```

在 Android 7.1.1 以上需要执行两条指令：

```
adb shell "settings put global captive_portal_http_url http://captive.v2ex.co/generate_204"; 
adb shell "settings put global captive_portal_https_url https://captive.v2ex.co/generate_204";
```

在 Android 8.x 后，加入了两个 fallback 的 Captive portal server 参数[^2]：

```
CAPTIVE_PORTAL_FALLBACK_URL
CAPTIVE_PORTAL_OTHER_FALLBACK_URLS
```

同样可以使用 settings 命令进行设置，格式为：

```
adb shell "settings put global captive_portal_fallback_url http://captive.v2ex.co/generate_204"; 
adb shell "settings put global captive_portal_other_fallback_urls http://captive.v2ex.co/generate_204"; 
```

本文根据v2ex的captive portal说明改编。

[^2]: 源码详见：[这里](http://androidxref.com/8.0.0_r4/xref/frameworks/base/services/core/java/com/android/server/connectivity/NetworkMonitor.java#675)

[^adb]: adb （ Android Debug Bridge ）是一个功能强大的命令行工具，属于 Google 官方的 Android 开发工具之一。你可以使用它来修改 Android 手机的一些核心设置。