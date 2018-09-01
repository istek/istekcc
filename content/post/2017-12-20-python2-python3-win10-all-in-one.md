---
title: python2和python3共存
date: 2017-12-20 10:35:30
layout: post
tags: 
  - python
---
##　安装python3

安装路径：`C:\Python\Python36`，注意选择配置环境变量。

## 安装python2

安装路径：`C:\Python\Python27`，不要选择配置环境变量，稍后手工配置。

## 环境变量

环境变量的配置看下图：
<!--more-->
![](https://qn.zhoutao.ren/TIM图片20171219230024.png)

## 修改文件名

将python36目录下的`python.exe`和`pythonw.exe`改为`python3.exe`和`pythonw3.exe`。

将python27目录下的`python.exe`和`pythonw.exe`改为`python2.exe`和`pythonw2.exe`。

## 换源

在windows资源管理器下键入`%APPDATA%`，新建文件夹`pip`，在`pip`文件夹内，新建文件`pip.ini`，以记事本形式打开，然后敲入下面的内容：

```ini
[global]
timeout = 6000
index-url = https://pypi.douban.com/simple
```

保存，如下图：

![](https://qn.zhoutao.ren/TIM截图20171219225508.png)

## 重新安装pip2和pip3

```
python3 -m pip install --upgrade pip --force-reinstall
python2 -m pip install --upgrade pip --force-reinstall
```