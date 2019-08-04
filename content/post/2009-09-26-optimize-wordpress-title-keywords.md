---
title: WordPress标题和KEYWORDS优化
date: 2009-09-26T06:58:07+00:00
layout: post
categories:
  - WEB
tags:
  - wordpress
---

之前一直使用的是All in one SEO pack插件，作为SEO优化，但是发觉百度收录情况很糟糕，百度只收录了首页，后来多方查证发现是All in one SEO pack插件会生活一个noindex标签，导致百度不能收录，于是找方法修改header文件，达到根据日志tag生成keywords的目标。下面就是代码了，各位朋友可以将代码添加到header.php文件中，替换掉以前的title标签。
```
    post_title ;
     $keywords = “”;
     $tags = wp_get_post_tags($post->ID);
     foreach ($tags as $tag ) {
     $keywords = $keywords . $tag->name . “, “;
     }
     } elseif(is_category()){
     $description = category_description();
     }
     ?>
```

title标签要替换掉header.php里面的标签就可以了。具体效果，大家请看本博客就清楚了。首页，日志，分类，很清楚的。