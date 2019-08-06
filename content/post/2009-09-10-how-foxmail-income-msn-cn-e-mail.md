---
title: 如何用foxmail收msn.cn邮件
date: 2009-09-10T05:10:09+00:00
layout: post
categories:
  - Windows
tags:
  - hotmail
---

我不想使用Windows Live Mail收取msn信箱，寻找一个能用foxmail接受MSN或者HOTMAIL信箱的软件，搜索了半天，终于[找到](http://hi.baidu.com/habbyn/blog/item/4553c0fcb6a598fdfc037fc9.html)了。

1. 下载FreePOPs，软件的官方网站是<http://www.freepops.org/，FreePOPs当前版本是0.2.9，纯绿色软件，直接解压缩到任意目录下即可使用。>
2. 下载hotmail插件（[hotmail.lua](http://www.freepops.org/download.php?module=hotmail.lua)）。
3. 将hotmail.lua拷贝到FreePOPs目录中的LUA文件夹中。
4. 打开Foxmail，添加你的msn信箱。

> POP：`127.0.0.1`
> SMTP：`smtp.msn.com` (由于FreePOPs不支持SMTP协议发送邮件，所以就这样填写了)
> Port：2000
> username：xxxxxx@msn.com

记得勾选SMTP认证。
<!--more-->
如果使用FreePOPs无法收信，请检查防火墙是否禁止了FreePOPs的网络连接权限。FreePOPs目前还不支持代理，用代理不能收信。由于Hotmail会不定期的更改其lua，可能会导致一段时间后出现FreePOPs无法使用，不断提示“密码错误”的状况。这个时候只要更新相应的[Lua](http://www.freepops.org/en/viewplugins.php)文件即可。

**自定义收取MSN.CN，Live.cn信箱**

1.freepop目录下有一个config.lua文件，选择记事本方式打开。

2.找到以下内容后
```
     — hotmail
     freepops.MODULES_MAP[“hotmail.com”] = {name=”hotmail.lua”}
     freepops.MODULES_MAP[“hotmail.de”] = {name=”hotmail.lua”}
     freepops.MODULES_MAP[“hotmail.it”] = {name=”hotmail.lua”}
     freepops.MODULES_MAP[“hotmail.co.uk”] = {name=”hotmail.lua”}
     freepops.MODULES_MAP[“hotmail.co.jp”] = {name=”hotmail.lua”}
     freepops.MODULES_MAP[“hotmail.fr”] = {name=”hotmail.lua”}
     freepops.MODULES_MAP[“msn.com”] = {name=”hotmail.lua”}
     freepops.MODULES_MAP[“webtv.com”] = {name=”hotmail.lua”}
     freepops.MODULES_MAP[“charter.com”] = {name=”hotmail.lua”}
     freepops.MODULES_MAP[“compaq.net”] = {name=”hotmail.lua”}
     freepops.MODULES_MAP[“passport.com”] = {name=”hotmail.lua”}
     freepops.MODULES_MAP[“messengeruser.com”] = {name=”hotmail.lua”}
```

添加：
```
 freepops.MODULES_MAP[“live.cn”] = {name=”hotmail.lua”}
 freepops.MODULES_MAP[“msn.cn”] = {name=”hotmail.lua”}
```

3.保存文件，退出。然后启动Freepops就可以接受msn.cn和Live.cn信箱了。

FreePOPs支持跨平台，有MacOX，Linux，Windows和BeOS操作系统。