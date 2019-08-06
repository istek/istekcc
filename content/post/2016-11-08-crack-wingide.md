---
title: WingIDE的特别烹制办法(☆_☆)/~~
date: 2016-11-08T21:12:55+00:00
layout: post
categories:
  - Windows
tags:
  - wingide
  - python
---
WingIDE是Python程序语言设计的集成开发环境，具有语法标签高亮显示，命令自动完成，函数跳转列表等非常强大的功能。

**一、下载WingIDE**

从官方网站下载最新版本，传送门[这里](http://wingware.com/)，目前最新版本是5.1.12-1。

**二、安装WingIDE**

按照正常流程安装即可，没有什么需要注意的地方。

_注意：WingIDE不内建python，所以需要在安装WingIDE之前先安装python，Windows下面推荐安装[activePython](http://www.activestate.com/activepython)。_
<!--more-->
**三、特别“烹制”**

在crack之前，需要保存一个python脚本到电脑上，脚本如下：
```
import sha
import string
BASE2 = '01'
BASE10 = '0123456789'
BASE16 = '0123456789ABCDEF'
BASE30 = '123456789ABCDEFGHJKLMNPQRTVWXY'
BASE36 = '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ'
BASE62 = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789abcdefghijklmnopqrstuvwxyz'
BASEMAX = string.printable
def BaseConvert(number, fromdigits, todigits, ignore_negative = True):
    """ converts a "number" between two bases of arbitrary digits

    The input number is assumed to be a string of digits from the
    fromdigits string (which is in order of smallest to largest
    digit). The return value is a string of elements from todigits
    (ordered in the same way). The input and output bases are
    determined from the lengths of the digit strings. Negative
    signs are passed through.

    decimal to binary
    >>> baseconvert(555,BASE10,BASE2)
    '1000101011'

    binary to decimal
    >>> baseconvert('1000101011',BASE2,BASE10)
    '555'

    integer interpreted as binary and converted to decimal (!)
    >>> baseconvert(1000101011,BASE2,BASE10)
    '555'

    base10 to base4
    >>> baseconvert(99,BASE10,"0123")
    '1203'

    base4 to base5 (with alphabetic digits)
    >>> baseconvert(1203,"0123","abcde")
    'dee'

    base5, alpha digits back to base 10
    >>> baseconvert('dee',"abcde",BASE10)
    '99'

    decimal to a base that uses A-Z0-9a-z for its digits
    >>> baseconvert(257938572394L,BASE10,BASE62)
    'E78Lxik'

    ..convert back
    >>> baseconvert('E78Lxik',BASE62,BASE10)
    '257938572394'

    binary to a base with words for digits (the function cannot convert this back)
    >>> baseconvert('1101',BASE2,('Zero','One'))
    'OneOneZeroOne'

    """
    if not ignore_negative and str(number)[0] == '-':
        number = str(number)[1:]
        neg = 1
    else:
        neg = 0
    x = long(0)
    for digit in str(number):
        x = x * len(fromdigits) + fromdigits.index(digit)

    res = ''
    while x > 0:
        digit = x % len(todigits)
        res = todigits[digit] + res
        x /= len(todigits)

    if neg:
        res = '-' + res
    return res

def SHAToBase30(digest):
    """Convert from a hexdigest form SHA hash into a more compact and
    ergonomic BASE30 representation.  This results in a 17 'digit'
    number."""
    tdigest = ''.join([ c for i, c in enumerate(digest) if i / 2 * 2 == i ])
    result = BaseConvert(tdigest, BASE16, BASE30)
    while len(result) < 17:
        result = '1' + result

    return result
def AddHyphens(code):
    """Insert hyphens into given license id or activation request to
    make it easier to read"""
    return code[:5] + '-' + code[5:10] + '-' + code[10:15] + '-' + code[15:]

LicenseID='CN123-12345-12345-12345'
#Copy the Request Code from the dialog
RequestCode='RW51D-H2H9H-C1565-6EY29'
hasher = sha.new()
hasher.update(RequestCode)
hasher.update(LicenseID)
digest = hasher.hexdigest().upper()
lichash = RequestCode[:3] + SHAToBase30(digest)
lichash=AddHyphens(lichash)

#Calculate the Activation Code
data=[7,123,23,87]
tmp=0
realcode=''
for i in data:
    for j in lichash:
        tmp=(tmp*i+ord(j))&0xFFFFF
    realcode+=format(tmp,'=05X')
    tmp=0

act30=BaseConvert(realcode,BASE16,BASE30)
while len(act30) < 17:
    act30 = '1' + act30
act30='AXX'+act30
act30=AddHyphens(act30)
print "The Activation Code is: "+act30
```

注意脚本中的LicenseID和RequestCode，在激活时输入LicenseID，然后将获得的RequestCode替换脚本中的字符串，然后运行脚本，就获得了ActivationCode。

1. 运行wingIDE，在激活窗口输入 CN123-12345-12345-12345
![1](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1478581543/042514_0349_WingIDE1_ojutgi.png)
2. 点击Continue后弹框，拷贝对话框中的RequestCode。
![2](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1478581683/042514_0349_WingIDE2_lds8pp.png)
3. 根据获得的RequestCode，替换脚本中的RequestCode里面的字符串，然后运行python脚本，就可以获得activationcode，填入就注册成功了！

**注意：**

目前新版本6.X，以上方法已经失效了，需要使用新的python脚本生成激活码。下载附件激活码，然后在输入license ID的窗口，执行脚本，会生成一个license ID，然后输入对话框，点击继续，然后或者Request Code，将Request Code贴入python终端，回车，获得激活码。已经使用了，可以正常激活目前6的最新版。

[下载地址](http://www.easysend.to/3eq)   [下载地址-onedrive](https://1drv.ms/u/s!Amx6iioDg2ithHErwisuK6Pvwnn6)