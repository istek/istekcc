---
title: Howto use Pubkey Authtication under CentOS5
date: 2010-06-04T16:31:51+00:00
layout: post
categories:
  - Linux
tags:
  - openssh
---

This’s a article show you howto use pubkey authtication under CentOS. Ok, let’s continue.

First, we need enable Pubkey Authtication.
```
vi /etc/ssh/sshd_config
```

uncomment and modify these lines below:
```
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
PasswordAuthentication no
```
<!--more-->
save file and quit vi. what we did is tell ssh daemon to use PubkeyAuthentication instead of traditional PasswordAuthenrication. avoid middleman attack.

right now restart ssh daemon service.use command below:
```
service sshd restart
```

Second, generate our pubkey through this command `ssh-keygen`. and i think you should have a good habit, when you find an unknown well command, you should use man to understand and know it.
```
ssh-keygen -t dsa
```

okay, follow instructions to complete your key generation.

right now we have 2 files under `/home/USERNAME/.ssh`, named `id_dsa` and `id_dsa.pub`. what should we do next? set proper permission and rename id_dsa.pub to authorized_keys.
```
mv /home/USERNAME/.ssh/id_dsa.pub /home/USERNAME/.ssh/authorized_keys
chmod 644 /home/USERNAME/.ssh/authorized_keys
chmod 700 /home/USERNAME/.ssh
```

After that, download your **id_dsa** for secureCRT by any SFTP clients or scp. Well, right now we have done with Server’s work. Below it’ll show you how to use pubkey to log in your Server.

Open SecureCRT, Click “New Session”, fill with your server’s ip, specified port, your username, click ok to close dialog window. Then, right click the session you created just now, click Properties, and choose SSH2 Category on the left, authentication area, uncheck PasswordAuthentication, single click “PublicKey”, then click “Properties”, “Use Identify or certificate file”, browser your id_dsa downloaded just now, click ok to close dialog.

Well, i think you can connect to your server without any password. Of course, for secure reason, you still need set passphrase and keep your files id_dsa and id_dsa.pub safe. Hypothetically, if you have more then one server, you can use your id_dsa.pub as many as you wish.