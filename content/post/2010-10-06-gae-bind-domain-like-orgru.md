---
title: GAE搭建博客中域名绑定设置
date: 2010-10-06T08:53:38+00:00
layout: post
categories:
  - WEB
tags:
  - Google
---
我也不想长篇大论讲如何申请pp.ru或者org.ru的域名，以为网上很多热心人已经有很多图文教程了。参考成功注册俄罗斯域名pp.ru等，由于每小时仅有4个，所以靠点运气。除此之外，主意在申请的时候填写的DNS server。第一次使用everydns.net,没有通过。后来在使用Zoneedit.com.才正常通过。

先到Zoneedit.com 注册一个呗，具体可以参考：Zoneedit免费老牌DNS代理 或者使用 Godaddy免费域名解析。当你使用Zoneedit.com提供的两个DNS Server:例如我的是： ns9.zoneedit.com 和 ns13.zoneedit.com。当然你需要在申请域名的时候在nserver这一栏天上以上两个server。就可以通过了。如果已经提交了域名申请，想更换dns server，可以给ru-dns@ripn.net发纯文本的电子邮件：同样你可以再在成功注册俄罗斯域名pp.ru等 找到具体方法。

好了，那么假设你已经申请好了域名，比如我的www.leyond.org.ru，一般是收到有delegated的一封邮件之后。现在要做的是在GAE中绑定这个域名。这个可以参考：Google GAE 绑定自己的域名 或者 Google App Engine绑定域名详解（图文版）, 绑定的时候需要注册一个Goole企业套件（都有点晕了，好多步骤！），参考： Google 企业套件申请攻略 （其实我觉得最有用的就是域名验证那部分。）申请好了之后，会要求开始验证你的域名。这里我采用更改CNAME记录来验证。可以参考：创建CNAME记录 ，以我的为例
<!--more-->
进入DNS管理，即登入zoneedit.com
<!--more-->
在Aliase (CNAME)选项中添加一条记录：`googlefffggghhh12345 is another name for google.com `

然后可以登入www.dirs.cn 验证 <http://googleffggghhh12345.leyond.org.ru>.

验证估计需要点时间。接着如果需要使用www.leyond.org.ru访问你的博客，你还需要添加一条CNAME记录。

这次Aliases留空，Actual Name填写ghs.you8g.com (这之前你还需要去免费GHS代理服务：http://www.you8g.com 登记的域名，这个简单~，使用这个是由于ghs.google.com被传说的防火墙PB了) 可以参考Zoneedit免费老牌DNS代理.这样就增加两条Cname记录 leyond.org.ru is another name for ghs.you8g.com 和www.leyond.org.ru is another name for ghs.you8g.ru

最后一步增加 Ip address A: 我跟他们一样填写的是：你 Ping www.leyond.org.ru就知道了。

然后我们就可以登入Google 企业应用套件控制台,比如我的 http://google.cn/a/leyond.org.ru, 登入之后可以看到左下角有一个类似：codingforit App Engine – 有效[codingforit是我的google app identifier].点击这个连接可以增加和删除网络连接。之后选择添加新网址：<http://www.leyond.org.ru> 大功告成。可能中途需要一些时间等待，比如域名申请，解析等都要时间，还有就是域名验证，我不知上面写着是48小时，我第二天就可以了。

写的太仓促，而且有些地方，我只是依葫芦画瓢，难免有疏漏和不足之处，多多指正。