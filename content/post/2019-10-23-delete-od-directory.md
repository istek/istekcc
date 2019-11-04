---
title: "删除OneDrive同步文件夹"
date: 2019-10-23T18:17:32+08:00
draft: false
categories:
 - Windows
tags:
 - OneDrive
featured_image: 
---

在重装操作系统以后，OneDrive文件夹不允许删除，显示没有权限这样的信息，虽然从安全标签里面修改为自己了，但是仍然无法删除，后来google了一下，发现下面的这个命令非常好使。

```
Remove-Item "OneDrive folder name" -Recurse -Force
```