---
title: 关于Windows Live Mail与Windows Mail 不能向新闻组发贴的解决
date: 2008-09-30T01:56:56+00:00
layout: post
categories:
  - Linux
tags:
  - wlm
---

随着Win VISTA的普及，不少用户选择了Windows Mail与Windows Live Mail替代原来OE，但不少朋友反映，Windows Mail与Windows Live Mail在访问一些国内新闻组（如新帆新闻组）的时候出现能够同步，不能发送的现象！错误代码如下：
```
Windows Mail 无法张贴您的邮件。 主题:（新闻组帖子主题）, 帐户: ‘news.newsfan.net’, 服务器: ‘news.newsfan.net’, 协议: NNTP, 服务器响应: ‘441 Feedrule: No newsgroups matched {=?gb2312?b?vmbl47v6ls34wucuv+20+m34?=}’, 端口: 119, 安全(SSL): 否, 服务器错误: 441, 错误号: 0x800CCCA9
```

解决方法很简单，对比OE与Windows Live Mail ， Windows Mail 的新闻组发送格式设置，会现在他们的发送编码选择不同！我们可以通过工具”－”选项”－”发送”－”新闻发送格式”－”纯文本格式设置 将Windows Live Mail ， Windows Mail 发送编码修改为**Uuencode**编码，即可发送！
