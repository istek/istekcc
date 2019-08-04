---
title: 让Google的新版界面不再一闪而过
date: 2006-04-04T12:11:54+00:00
layout: post
categories:
  - WEB
tags:
  - Google
---

首先进入Google.com，然后搜索任意关键词，进入了搜索结果页。这时，将下面的代码复制到浏览器的地址栏里，代替原来的地址，然后按回车（注意，下面的代码只有一行，并没有换行，换行不会成功）：

```
javascript:alert(document.cookie=”PREF=ID=fb7740f107311e46:TM=1142683332:LM=1142683332:S=fNSw6ljXTzvL3dWu;path=/;domain=.”+location.href.split('/')[2].substr(location.href.split('/')[2].indexOf('google')));
```

这时会弹出一个对话框，不管它，点确定即可。然后再刷新一下当前的网页即可看到新版的Google搜索页了。这不但是一次，你以后搜索时，都会是这个新版的网页风格。

恢复旧google页面的方法：

同上，在地址栏里输入上面的代码，回车后马上关闭浏览器，这样新的cookie就失效了。