---
title: "Office OneDrive无法同步文件"
date: 2019-07-10T11:04:45+08:00
draft: false
layout: post
tags: 
  - Onedrive
categories:
  - Windows
---

昨天我发现我这边无法同步OD的文件，感觉很奇怪，因为先前都是工作正常的，只能怀疑要么是被墙了，要么就是我电脑的问题。

今天在OFFICE群组和众人沟通OD的连接问题，原来大家都不能访问了，看来是真的被墙了。唉，也不知道一个办公套件的OD究竟哪里不对了。

目前来看，解决办法有两种：一是修改hosts文件；二是梯子+Proxifier工具。
<!--more-->

# 一、修改hosts文件
打开 `C:\Windows\System32\drivers\etc` 目录，使用你喜欢的文本编辑器打开`hosts`文件，添加如下内容：

```
# OneDrive Start
204.79.197.213	api.onedrive.com
23.76.75.24	api.onedrive.live.com
131.253.33.217	onedrive.live.com
131.253.33.217	webedgegeo.skyprod.akadns.net
131.253.14.230	skyapi.onedrive.live.com
134.170.104.157	skyweb.skyprod.akadns.net
# OneDrive End
```

然后保存文件，现在退出你的OD客户端，重新打开，看到一朵祥云了没？

# 二、使用梯子+Proxifier

梯子就不多说了，自己想办法，建议使用速度快的梯子，这样对于上传下载文件肯定有好处。

[下载地址](https://pan.istek.cc/down/004.other_software/Proxifier_342.zip "Proxifier")

1、添加代理服务器

![](https://raw.githubusercontent.com/istek/img/master/img20190710111959.png)

![](https://raw.githubusercontent.com/istek/img/master/img20190710112051.png)

![](https://raw.githubusercontent.com/istek/img/master/img20190710111852.png)

2、添加代理规则

![](https://raw.githubusercontent.com/istek/img/master/img20190710112132.png)

添加一条onedrive的规则
点击浏览，查找OD可执行文件，一般该文件在`C:\Users\[UserName]\AppData\Local\Microsoft\OneDrive\OneDrive.exe`

![](https://raw.githubusercontent.com/istek/img/master/img20190710112231.png)

调整默认规则，默认情况下，都直连，除非你有其他需求。

![](https://raw.githubusercontent.com/istek/img/master/img20190710112457.png)

OK了，现在关闭OD客户端，重新打开OD客户端，你会发现OD已经连接上了。
