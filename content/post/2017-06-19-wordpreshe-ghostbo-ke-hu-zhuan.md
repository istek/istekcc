---
title: Wordpres和Ghost博客互转
date: 2017-06-19T12:29:50+00:00
layout: post
categories:
  - WEB
tags:
  - ghost
  - wordpress
---
前两天将自己的博客由wordpress转为ghost，google的过程是痛苦的，因为要验证是否可行，参考了很多文章[^1]，最后搞定了。

# 一、Wordpress转Ghost

## 1、文章

在Wordpress安装[Ghost导出插件](https://wordpress.org/plugins/ghost/)，安装后，启用该插件，从‘工具’-‘Export to Ghost’导出文章内容。

![1](https://www.ghostforbeginners.com/content/images/2013/10/Ghost-Import-1.png)

_注意：这里仅能导出文章（post）和独立页面（page），无法导出分类，所以必须要先将分类转为tags。_

点击`Download Ghost File`，导出ghost能够导入的json文件。
<!--more-->

## 2、评论

因为Ghost没有评论系统，所以需要使用第三方评论系统，比如，[disqus](https://www.disqus.com) ,[网易云跟帖](https://gentie.163.com/)。这里我们以disqus为例。

首先，注册disqus账号。

其次，增加一个disqus站点，添加你的域名，

![TIM截图20170619112015](https://qn.zhoutao.ren/2017/06/timjie-tu-20170619112015.png)

之后，在wordpress安装[disqus插件](https://wordpress.org/plugins/disqus-comment-system/)，以你的disqus账号登陆成功后，有一个`Import and Export`设置项目，点击`Export comments to Disqus`后面的`Export Comments`，这样就把wordpress中的评论全部导出到disqus了。

![TIM截图20170619112619](https://qn.zhoutao.ren/2017/06/timjie-tu-20170619112619.png)

### 3、文件

将主机`wordpress/wp-content/uploads`目录下面的所有文件复制到`ghost/content/imges`目录。然后修改你导出的JSON文件里面的附件路径。

```
sed -i .bak -e 's|wp-content\\/images|content\\/images|g' wp2ghost_export_1418184250.json
```

### 4、导入

之后将我们编辑过的json文件打包为zip文
```
zip wp2ghost.zip wp2ghost_export_1418184250.json
```

进入Ghost后台的Labs菜单，点击`Import`下的`choose file`，选择zip压缩文件，然后`import`

![2](http://help.ghost.org/hc/en-us/article_attachments/212833487/import-into-ghost.png)

### 5、给Ghost增加disqus评论系统

编辑`ghost/content/theme/casper/post.hbs`文件，在`</footer>`和`</article>`之间添加如下代码

```
<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/

var disqus_config = function () {
    this.page.url = '{{url absolute="true"}}';
    this.page.identifier = 'ghost-{{id}}';
};

(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://你的disqus网站名.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
```

注意：代码中**你的disqus网站名**，这个填你自己的网站名。

编辑`ghost/content/themes/casper/default.hbs`文件，在`</body>`之前增加如下代码

```
<script id="dsq-count-scr" src="//你的disqus网站名.disqus.com/count.js" async></script>
```

编辑`ghost/content/themes/casper/partials/loop.hbs`文件，在`</footer>`之前添加如下代码

```
(<a href="{{url}}#disqus_thread">Comments</a>)
```

修改好后，重启你的Ghost！否则，无法生效，看不到效果。

**注解：**

1. post.hbs添加的代码，是在文章页显示评论界面。
1. default.hbs添加的代码，是展示评论数的js文件。
1. loop.hbs添加的代码，是为了在首页展示评论数。

## 二、Ghost转Wordpress

### 1、导出JSON

点击`Labs`菜单，点击 `Export` 按钮，导出json文件。

![2](https://qn.zhoutao.ren/2017/06/timjie-tu-20170619121013.png)

### 2、导入wordpress

下载`ghost2wp`这个python脚本，经我验证，文章可以完整导入。这个脚本它是解析json文件后，使用XML-RPC协议将所有文章和页面导入wordpress，个人觉得还是挺完美的，貌似无法导入tags，因为我用我的博客实验的，导入后没有tags。它使用了`python-wordpress-xmlrpc`这个模块，要求版本必须大于等于2.3。

```
git https://github.com/jbfriedrich/ghost2wp.git
```

然后将json文件放到和ghost2wp.py同级的目录，之后运行ghost2wp.py文件

```
python ghost2wp.py -e http://example.com/xmlrpc.php -f xxx.json -u WP用户名 -p 'WP密码'
```

## 三、其他

其实ghost还可以导出为markdown文件，然后使用在其他的静态类博客程序中。比如，ghost2hexo，这是我也试验了，确实可以将所有文章，用slug作为文件名，导出为markdown文件。ghost博客还是挺好使的，和其他的php博客相比，资源占用小，目标明确，就是一个单纯的blog程序，像wordpress，就不应该属于blog程序了，因为太庞大，应该定义到CMS的范畴。



[^1]: <https://www.ghostforbeginners.com/migrating-your-wordpress-blog-to-ghost/> https://help.ghost.org/hc/en-us/articles/225093168-Migrating-From-WordPress-to-Ghost


