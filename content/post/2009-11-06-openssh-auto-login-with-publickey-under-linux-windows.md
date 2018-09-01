---
title: 基于公钥认证方式的 OpenSSH Server 自动登录完全手册
date: 2009-11-06T01:15:53+00:00
layout: post
categories:
  - 我的世界
tags:
  - linux
  - openssh
---

## 概述

假设要以用户 rainux 的身份登录运行 OpenSSH Server 的远程主机 www.rainux.org（现在几乎所有的 Linux 服务器都使用 OpenSSH Server 作为 SSH Server），那么需要做的操作如下：

1.生成用于 SSH 身份认证的密钥。密钥由公钥和私钥组成，一个公钥只对应一个私钥，一个私钥也只对应一个公钥。
2.将 OpenSSH 格式的公钥文本追加到远程主机上的 `~/.ssh/authorized_keys` 文件里（~ 意为用户 rainux 的 HOME 目录）。注意公钥文本中应该没有换行符，一行就是一个公钥。
3.确保 `~/.ssh/authorized_keys` 权限为 600，`~/.ssh` 目录权限为 700，并且它们的所有者都是 rainux。
4.指定 SSH 客户端使用对应的私钥并以用户 rainux 的身份登录 www.rainux.org，此时无需输入 rainux 在 www.rainux.org 上的密码。
<!--more-->
注意：如果私钥是以加密形式存储的（强烈建议使用一个强壮的密码加密私钥，这样即使私钥文件被窃取，也无法被直接使用），第四步仍然需要输入加密私钥所用的密码。但是 PuTTY 和 OpenSSH 都有提供一个代理程序用于避免多次重复输入密码。运行代理程序后将私钥添加到其中，并且输入一次加密私钥所用的密码，然后保持代理程序的运行，以后 SSH 客户端即可在需要使用私钥进行身份认证的场合请求代理程序去完成身份认证（这是为了确保私钥不会被泄漏到任何使用代理程序的客户端上），无须再次输入任何密码。

## Linux / Cygwin / MSYS 上使用 OpenSSH Client

### 生成密钥

执行 ssh-keygen 接受默认文件名，并且输入加密私钥用的密码（passphrase）即可生成私钥`~/.ssh/id_rsa` 和对应的公钥`~/.ssh/id_rsa.pub`。其中`id_rsa.pub`的内容可以直接追加到远程主机的 `authorized_keys`文件里。

### 使用私钥

把公钥部署到远程主机上后，即可直接执行`ssh rainux@www.rainux.org`来登录远程主机。但如果私钥是加密形式保存，如上所述应该使用代理程序`ssh-agent`来避免每次输入密码。

直接执行`ssh-add`命令可以添加刚才生成的私钥到`ssh-agent`里，也可以用`ssh-add -l`查看已经添加的私钥。如果执行`ssh-add`时提示无法连接身份认证代理，则需要使用以下命令来启动`ssh-agent`。
```
eval `ssh-agent`
eval `ssh-agent`
```

使用`eval`是为了执行ssh-agent输出的设置环境变量的bash命令，以确保ssh-add可以通过`SSH_AUTH_SOCK`环境变量找到 ssh-agent。

GNOME 的桌面系统如果有安装 gnome-keyring，它会自动管理 ssh-agent，通常甚至无须手工添加私钥到 ssh-agent 里，第一次使用公钥时 gnome-keyring 就会提示输入一次密码，以后则不再需要。

## Windows 上使用 PuTTY

### 生成密钥

到 PuTTY 的下载页面下载 putty-0.60-installer.exe 或者 putty.zip，两者都包含 PuTTY 所有的组件。

安装好 PuTTY 之后（对于 zip 版本来说，解压到任意目录即可）运行 PuTTYGen 生成自己的密钥。选择密钥类型为 SSH-2 RSA，点击 Generate，按照提示在 PuTTYGen 窗口内随机移动鼠标直到进度条达到 100%，然后 PuTYYGen 会生成密钥并且显示其公钥部分信息。这里可以填写一个有意义的注释，然后输入一个用来加密私钥的强壮的密码（Key passphrase）。最后点击 Save private key 将私钥保存为一个 .ppk 文件。不需要单独 Save public key，因为 .ppk 文件里已经包含了公钥。并且通过 Save public key 保存出来的公钥文件格式与 OpenSSH 的格式并不相同，若要将公钥部署到使用 OpenSSH Server 的服务器上，只能使用 PuTTYGen 窗口上显示的那一段 Public key for pasting into OpenSSH authorized_keys file。

### 使用私钥

将公钥部署到远程主机上之后，使用 PuTTY 登录该远程主机时可以在 Connection > SSH > Auth 里选择用于身份认证的私钥文件。同样，如果私钥是加密形式保存，应该使用代理程序 Pageant 以避免每次输入密码。

最简单的办法是创建一个 Pageant 的快捷方式，并且将 .ppk 私钥文件作为参数加到快捷方式的“目标”栏里，添加之后看上去应该像这样：
```
 C:\PuTTY\pageant.exe D:\main.ppk "D:\My Secrets\secondary.ppk"
 C:\PuTTY\pageant.exe D:\main.ppk "D:\My Secrets\secondary.ppk"
```

就像上面的例子一样，如果私钥文件路径中带有空格，应该使用引号将其括起来。通过这个快捷方式启动 Pageant 则会自动装载指定的私钥文件，并且立即询问用于加密私钥的密码。以后只要保持 Pageant 的运行，并且在 PuTTY 的某个 Saved session 里设置 Connnection > Data 里设置 Auto-login username，即可实现双击该 session 则完全自动登录。