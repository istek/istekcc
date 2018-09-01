---
title: 在线播放FLV文件的代码
date: 2007-04-07T14:43:07+00:00
layout: post
categories:
  - 我的世界
---
最近经常看到的都是FLASH这样的MTV，下载下来还需要特别的播放器，麻烦的很，还不如发表在博客里面呢，下面的这个文章是我从网络上找到的，呵呵，留下来，感谢作者。

首先知道Google播放器的地址：
```
http://video.google.com/googleplayer.swf
```

然后找到 flv文件的地址，例如：
```
http://v1.ouou.com/BAB51A4D332E42A1/_01/data/video_source/videos/oubj/2007/02/01/1170295437055.flv
```

<!--more-->

然后找到你想要在开始播放前显示的缩略图例如：
```
http://www.eooeo.com/IMAGES1/top2.jpg
```

最终形成两种代码：

有缩略图点击播放：
```
http://video.google.com/googleplayer.swf?videoUrl=http://v1.ouou.com/BAB51A4D332E42A1/_01/data/video_source/videos/oubj/2007/02/01/1170295437055.flv&thumbnailUrl=http://www.eooeo.com/IMAGES1/top2.jpg&playerMode=normal


http://video.google.com/googleplayer.swf?videoUrl=http://v1.ouou.com/BAB51A4D332E42A1/_01/data/video_source/videos/oubj/2007/02/01/1170295437055.flv&thumbnailUrl=http://www.eooeo.com/IMAGES1/top2.jpg&playerMode=normal&autoPlay=true
```

缩率图也可以不要：
```
http://video.google.com/googleplayer.swf?videoUrl=http://v1.ouou.com/BAB51A4D332E42A1/_01/data/video_source/videos/oubj/2007/02/01/1170295437055.flv
```

OK啦

下面就是一个小实验，嘿嘿。