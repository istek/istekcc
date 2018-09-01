---
title: “Chrome 101 (net::ERR_CONNECTION_RESET):未知错误”的解决办法
date: 2010-03-07T09:17:43+00:00
layout: post
categories:
  - 我的世界
tags:
  - Google
---
近来安装了chrome，当使用扩展程序，安装google的官方扩展程序时，总是出现错误提示：

错误 101 (net::ERR_CONNECTION_RESET):未知错误

在网上搜集了一下解决办法：

修改hosts文件，在`C:\Windows\System32\Drivers\Etc\hosts`加这么几行：
```
74.125.39.99 clients2.google.com
74.125.39.99 chrome.google.com
74.125.39.99 tools.google.com
2001:4860:8004::89 www.google.com
2001:4860:8004::89 images.google.com
2001:4860:8004::89 video.google.com
2001:4860:8004::89 news.google.com
2001:4860:8004::89 www.google.com.hk
2001:4860:8004::89 images.google.com.hk
2001:4860:8004::89 video.google.com.hk
2001:4860:8004::89 news.google.com.hk
```

问题解决.如果你的系统是Vista或Win7，有6to4隧道服务（默认打开），可以直接使用我说的方法，不需要有IPV6接入。

提示：编辑时，最好使用editplus这样的专业的文本文件来编辑，保存时不要加扩展名