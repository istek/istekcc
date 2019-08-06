---
title: 恢复显示桌面图标
date: 2005-12-13T03:36:29+00:00
layout: post
categories:
  - Windows
---

有时候我们会不经意的删除了快速显示桌面的图标，如果你不知道这个图标，那么。。。。此文就不用看了。呵呵。

“快速启动”栏中的“显示桌面”按钮不同于一般的快捷方式。如果不小心删除了“显示桌面”按钮，那么按照一般的方法是不能够恢复的。

要恢复“快速启动”栏中的“显示桌面”按钮需要采用如下所述的方法。

打开“记事本”程序，在其中输入如下内容：

```
[Shell]
Command=2
IconFile=explorer.exe,3

[Taskbar]
Command=ToggleDesktop
```

然后把这个文件保存为：`Show Desktop.scf`，必须确认文件名和双引号中的一样。

把`Show Desktop.scf`文件复制到：
`C:\Documents and Settings\用户名\Application Data\MicrosoftInternet Explorer\Quick Launch\`
目录下。

其中你需要把“用户名”替换成你需要恢复“显示桌面”按钮的用户名。