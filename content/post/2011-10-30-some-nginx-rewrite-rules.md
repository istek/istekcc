---
title: some nginx rewrite rules
date: 2011-10-30T23:18:18+00:00
layout: post
categories:
  - 我的世界
tags:
  - nginx
---

客户要用DISCUZ作为交流论坛，从网上找了一个DISCUZ规则，发现不对，现发出正确的规则。

```
rewrite ^([^.]_)/topic-(.+).html$ $1/portal.php?mod=topic&topic=$2 last;
rewrite ^([^.]_)/article-([0-9]+)-([0-9]+).html$ $1/portal.php?mod=view&aid=$2&page=$3 last;
rewrite ^([^.]_)/forum-(\w+)-([0-9]+).html$ $1/forum.php?mod=forumdisplay&fid=$2&page=$3 last;
rewrite ^([^.]_)/thread-([0-9]+)-([0-9]+)-([0-9]+).html$ $1/forum.php?mod=viewthread&tid=$2&extra=page%3D$4&page=$3 last;
rewrite ^([^.]_)/group-([0-9]+)-([0-9]+).html$ $1/forum.php?mod=group&fid=$2&page=$3 last;
rewrite ^([^.]_)/space-(username|uid)-(.+).html$ $1/home.php?mod=space&$2=$3 last;
rewrite ^([^.]_)/blog-([0-9]+)-([0-9]+).html$ $1/home.php?mod=space&uid=$2&do=blog&id=$3 last;
rewrite ^([^.]_)/(fid|tid)-([0-9]+).html$ $1/index.php?action=$2&value=$3 last; if (!-e $request_filename) { return 404; }
```
<!--more-->
wordpress的规则

```
if (!-e $request_filename){
	rewrite (.*) /index.php;
}
```

如果是2级域名，可以使用

```
location /blog/ {
	if (!-e $request_filename){
		rewrite (.*) /index.php;
	}
}
```
