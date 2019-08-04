---
title: Fix mbr record in windows xp
date: 2009-08-27T20:04:18+00:00
layout: post
categories:
  - windows
tags:
  - fixmbr
---
Yesterday i reinstalled my ubuntu, but i don’t know how to destory my mbr record. I’ve 2 hard drives, one is 120G IDE hard drive installed Ubuntu, the other is 250G SATA harddrive installed Window Xp. And usually 120G harddrive is my first boot disk, and grub should be installed there. But last night i installed Grub2, I don’t know how to rewrite my 250g disk’s mbr.

Okay,right now let me fix it. You need administrative privileges to make it work!

First,you need a tool named “MBR Fix”, [click here](http://rapidshare.com/files/272003046/mbrfix.zip.html) to download.

Second, extract it to a directory. You can find there’s only one command line tool.

Third, open your dos prompt, cd to the directory where your mbrfix locates.
<!--more-->
Forth, use this grammer to fix your mbr.
```
MbrFix /drive <numfixmbr {/vista|/win7}
```
Update MBR code to W2K/XP/2003, Vista or Win7

And here below is all available command:
```
MbrFix /drive <numdriveinfo Display drive information
MbrFix /drive <numdrivesize Returns drive size in MB as return value
MbrFix /drive <numlistpartitions Display partition information
MbrFix /drive <numsavembr <fileSave MBR and partitions to file
MbrFix /drive <numrestorembr <fileRestore MBR and partitions from file
MbrFix /drive <numfixmbr {/vista|/win7} Update MBR code to W2K/XP/2003, Vista or Win7
MbrFix /drive <numclean Delete all partitions on the selected disk
MbrFix /drive <numreadsignature {/byte} Read disk signature from MBR
MbrFix /drive <numwritesignature <hexWrite disk signature to MBR
MbrFix /drive <numgeneratesignature Generate disk signature in MBR
MbrFix /drive <numreadstate Read state from byte 0x1b2 in MBR
MbrFix /drive <numwritestate <stateWrite state to byte 0x1b2 in MBR
MbrFix /drive <numreaddrive <startsector<sectorcount<file Save sectors from drive to file
MbrFix /drive <num/partition <partfixbootsector <osUpdate Boot code in boot sector
MbrFix /drive <num/partition <partgetpartitiontype Get partition type

Set partition type

MbrFix /drive <num/partition <partsetactivepartition

Set active partition

MbrFix /drive <numgetactivepartition Get active partition
MbrFix volumeinformation driveletter Get volume information for partition
MbrFix flush {driveletter(s)} Flush files to disk for partition
MbrFix listpartitiontypes List partition types
```
