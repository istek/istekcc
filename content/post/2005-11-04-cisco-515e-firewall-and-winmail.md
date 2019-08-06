---
title: Cisco 515E防火墙与Winmail
date: 2005-11-04T03:18:24+00:00
layout: post
categories:
  - Windows
---

关于CISCO的端口和SMTP服务。

前几天安装了一个邮件服务器，从WEB上收发都是没有问题的，问题就是无法从OE发送邮件，开了身份验证，还是不行，后来从GOOGLE上搜索了一下错误代码，才意识到是防火墙的问题，我以前一直忽略了我们的硬件防火墙，从文章来看，是因为CISCO系列防火墙对扩展SMTP的支持是关闭的，所以无法进行身份验证这样的ESMTP活动。

打开Cisco 515e console,执行下面的命令

```
＃configure terminal //进入终端配置模式
＃no fixup protocol smtp 25  //关闭fixup smtp.
＃write mem //写入内存保存
```

就可以了。

最好能打开25端口

```
＃conduit permit tcp host IP eq smtp any  //允许any主机到IP:25的TCP连接
```

CISCO 515E好难配，我在console里面转了半天，才知道从哪里可以`no fixup`,`conduit`。晕，要学习的东西太多了。