---
title: 查看、变更IIS中IUSR_computername的密码
date: 2007-09-22T14:50:56+00:00
layout: post
categories:
  - 我的世界
tags:
  - windows
---

今天不知道怎么回事，IIS访问出现401.1访问凭据失效的错误。查看了IUSR帐号是启用的，也查看了IUSR帐号的目录权限，还有IIS匿名访问控制，都没有问题，很奇怪的问题，后来自己尝试修改了IUSR的帐号密码，和IIS匿名控制中IUSR的密码，这才好了。为什么会导致这样的问题呢？为什么会出现这样的问题？

下面的这个方法起到了很大的作用，帖在这里，以防以后使用。

`IUSR_computername`和`IWAM_computername`这两个帐户都是IIS中匿名访问使用的用户帐户，它们由系统控制密码，而且不能随意改动，今天我和大家一起来看一下它们的密码到底是什么。
<!--more-->
使用`adsutil.vbs`脚本可以查看和修改它们的密码，这个脚本文件位于`c:\Inetpub\AdminScripts`文件夹中。

1. 查看

要查看IUSR_computername的密码，可以运行：
```
cscript.exe adsutil.vbs get w3svc/anonymoususerpass
```

要查看IWAM_computername的密码，可以运行：
```
cscript.exe adsutil.vbs get w3svc/wamuserpass
```

当执行命令后，你会发现，密码仍然显示为`*`，这是因为密码默认受到了保护，要想查看，我们需要使用记事本修改一下`adsutil.vbs`脚本。

打开adsutil.vbs脚本后，在最后几行，找到`IsSecureProperty = True`，将值`True`修改为`False`，然后保存，再次运行查看命令。

1. 修改

密码同时存在SAM数据库中，要修改，先必须在[计算机管理]控制台中修改，然后使用adsutil.vbs命令修改。

修改IUSR_computername的密码，可以运行：
```
cscript.exe adsutil.vbs set w3svc/anonymoususerpass "P@ssw0rd"
```

修改IWAM_computername的密码，可以运行：
```
cscript.exe adsutil.vbs set w3svc/wamuserpass "P@ssw0rd"
```