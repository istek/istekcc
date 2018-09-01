---
title: How to Unroot your OnePlus 3 / 3T and go back completely to stock
date: 2017-06-05T19:13:07+00:00
layout: post
categories:
  - 我的世界
tags:
  - oneplus3
---

周末刷了一下自己的手机，用的TWRP的recovery，刷了一个RR的rom，用了一天感觉没有官方的版本好，于是就想刷回去，折腾了一上午，最后从一加国外官方站，找到了这篇救急的文章，终于把自己的加3刷回去了。太费劲了。用来用去，还是官方原版好使，唉，这就是折腾啊。

That being said, here are the instructions (all commands are typed on a computer using a terminal app (Linux or Mac), or Command Prompt in Administrator mode (Windows)):
<!--more-->
1. Download the current OxygenOS (Stable or Open Beta) full zip from the OnePlus support site: [http://downloads.oneplus.net/](http://downloads.oneplus.net/oneplus-3/oneplus_3_oxygenos_4.0.2/)
**PRO TIP: rename your downloaded file to ota.zip, it’ll make typing the commands a bit easier. _If you have a OnePlus 3, then download the OnePlus 3 files. If you have a OnePlus 3T, then download OnePlus 3T files._**
2. Download the stock recovery for your device:
- http://oxygenos.oneplus.net.s3.amazonaws.com/recovery_op3.img – OP3 Recovery
- http://oxygenos.oneplus.net.s3.amazonaws.com/recovery_op3t.img – OP3T Recovery
3. Boot into Fastboot Mode (I believe it’s power and volume up, or use the extended boot menu from Android to boot here)
4. Fire up a terminal app or the Windows Command Prompt in Admin mode and make sure you’re in the folder/directory where you downloaded the above files on your computer.
5. Type: **fastboot devices** — Just to make sure everything is working. If you see a serial number you’re golden. If not check all of your drivers (mostly Windows users will have this issue). There are threads on how to install the drivers within the forums. If you use Linux or a Mac you should already know how to get the proper files on there.
6. Type: **fastboot flash recovery recovery_op3.img (or recovery_op3t.img if you have a 3T)** — This will flash the stock recovery back to the phone.
7. Type: **fastboot oem lock**, then follow the prompts on the screen. This will erase the phone. The phone will boot into recovery with chinese characters and wipe the phone clean. When it’s done it’ll reboot, the second you see that happen hold Volume down to boot into recovery again. If you don’t it’ll be in an endless bootloop.
8. Boot into recovery by holding down power and volume down. It’ll boot into stock recovery.
9. Choose english as the language, very important
10. There’ll be an option to upgrade via sideload. Choose that option. This will put the phone into ADB sideload mode.
11. Type the following: **adb sideload ota.zip**
12. Go grab a cup of coffee or two. It’ll take a bit.
13. Once it’s done reboot the phone. The option will be there once the sideloading is done. Just select **Reboot** and press the power button.
14. It should boot into stock, unrooted OxygenOS. Congratulations you did it! Enjoy your stock, unrooted device!

> After you do the **fastboot oem lock** command and it erases the phone & reboots, it may bootloop once and then go dark. If that happens hold down the power button until it reboots, or hold down power and volume down until it reboots. It’s normal.