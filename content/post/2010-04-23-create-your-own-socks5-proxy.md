---
title: Create Your Own Socks5 proxy
date: 2010-04-23T03:32:23+00:00
layout: post
categories:
  - Linux
tags:
  - ssh
  - proxy
---

Do you know that you can create your own socks5 proxy with just one command:
```
ssh -f -N -D 5050 "USERNAME"@SSHserver.com
```

-f -N option is just to run ssh in background, the important option is -D.

from FreeBSD SSH(1) man:
```
    -D [bind_address:]port
```
<!--more-->
Specifies a local dynamic application-level port forwarding. This works by allocating a socket to listen to port on the local side.

For windows users they can do it with putty
```
C:\>putty.exe -ssh "USERNAME"@SSHserver.com -pw "PASSWORD" -P 22 -D 5050
```

what we did is that we connect to a ssh server (could by your remote linux or unix server, or a free shell account you have) and give it option to create a tunnel with port forwarding for port 5050

if you set your browser to use socks5 proxy with the address 127.0.0.1 and port 5050, the traffic will be forwarded to the tunnel and you will browse the internet through the remote ssh server.

to disable port forwarding in your server, edit `/etc/ssh/sshd_config` and set “_AllowTcpForwarding yes_” to “no”