---
title: MSLive信箱的收取办法
date: 2009-09-14T02:22:40+00:00
layout: post
categories:
  - 我的世界
tags:
  - foxmail
  - hotmail
---

那天我写了一篇日志,是关于使用FreePOPs收取msn,live信箱的[办法](http://www.stanleyzhou.org.ru/notes/how-foxmail-income-msn-cn-e-mail.html). 后来, 我发现我的办法其实已经过时了, 或者说根本不能适用于现在的LIVE和MSN信箱了, 其实MSN,LIVE是支持POP和SMTP的, 只是我们都没有发现而已.

话说昨天给我的域名申请live的[domain](http://domains.live.com)服务的时候, 我就想, 不可能微软还不开放POP和SMTP访问的, 因为他要做企业信箱, 肯定要考虑到企业使用信箱的便利性. 于是, 我打开帮助文档, 仔细查找关于远程访问的章节, 没有想到真的有所收获, 并且也在自己的站点上成功了, 我个人的MSN.CN的信箱也可以使用. 所以, 我觉得应该是通用的.

打开你的邮件客户端, 无所谓是哪种, Outlook Express或者是Foxmail, 添加一个新帐号, 输入你的LIVE或者MSN,  或者在是使用了Domains.live.com服务的邮箱地址. 现在我就以OE为例,将方法列出.
<!--more-->
1. 在 Outlook Express 中，单击“工具”菜单，然后单击“帐户”。
2. 单击“添加”，然后单击“邮件”。
3. 键入您的姓名，然后单击“下一步”。
4. 键入您的完整 Windows Live Hotmail 电子邮件地址，然后单击“下一步”。
5. 输入以下信息，然后单击“下一步”：
  * 在“我的邮件接收服务器是”框中，单击“POP3”。
  * 在“接收邮件(POP3、IMAP 或 HTTP)服务器”框中，键入“pop3.live.com”。
  * 在“发送邮件(SMTP)服务器”框中，键入“smtp.live.com”。
6. 键入您的 Windows Live Hotmail 帐户的帐户名和密码。如果您不是此计算机的唯一使用者，请清除“记住密码”复选框。请确保没有选中“使用安全密码验证登录(SPA)”复选框。
7. 单击“下一步”，然后单击“完成”。
8. 在“Internet 帐户”窗口中，单击“邮件”选项卡，然后单击您已添加的 Windows Live Hotmail 帐户，再单击“属性”。
9. 单击“高级”选项卡，并在“服务器端口号”下输入以下信息，然后单击“确定”：
  * 在“接收邮件(POP3)”框中，键入“995”。
  * 在“发送邮件(SMTP)”框中，键入“25”。
  * 在“发送邮件(SMTP)”和“接收邮件(POP3)”下，选中“此服务器要求安全连接(SSL)”复选框。
10. 单击“关闭”。

OK.现在试验一下收件和发件, 一路畅通无阻. pretty good, huh? 呵呵.