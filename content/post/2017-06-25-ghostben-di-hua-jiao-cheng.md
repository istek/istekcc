---
title: Ghost本地化教程
date: 2017-06-25T23:00:56+00:00
layout: post
categories:
  - 我的世界
tags:
  - ghost
---

众所周知的原因，当国外的一些好产品进入国内后，总会水土不服，让人体验不好，这不是产品的原因，相反，这是因为被我们的伟大的墙阻挡了。

# 1. Google字体

## 1.1 国内镜像

可以使用下面国内镜像，选一个即可。

<!--more-->

### 中科大

    //fonts.proxy.ustclug.org/

### css.network

    //fonts.css.network

## 1.2 修改办法

编辑`ghost/content/themes/casper/default.hbs`文件

将下面的这段代码的域名部分做修改
```
{{!-- Styles'n'Scripts --}}
<link rel="stylesheet" type="text/css" href="//fonts.lug.ustc.edu.cn/css?family=Merriweather:300,700,700italic,300italic|Open+Sans:700,400" />
```

改为
```
{{!-- Styles'n'Scripts --}}
<link rel="stylesheet" type="text/css" href="//fonts.css.network/css?family=Merriweather:300,700,700italic,300italic|Open+Sans:700,400" />
```

# 2. Gravatar 头像国内镜像

选用下面的一个就可以。

## 2.1 国内源

### CSS.NETWORK

```
https://gravatar.css.network/
```

### 给gravatar.com增加WWW前缀也可以

```
//www.gravatar.com
```

## 2.2 修改办法

gravatar主要是根据EMAIL地址显示头像，而email地址保存在数据库users表，修改image字段就可以。

```
mysql -uroot -p
mysql> select email,image from users;
+---------------------------------------------------------------------------+
    | email             | image                                         |
    +-----------------+------------------------------------+
    | imzhoutao@gmail.com |/gravatar.com/avatar/d9ec64e61ba31510cc96bc7eede646aa?s=250&d=mm&r=x |
    +-------------------+-------------------------------------------+
1 row in set (0.00 sec)
mysql> update users set image = '//www.gravatar.com/avatar/d9ec64e61ba31510cc96bc7eede646aa?s=250&d=mm&r=x' where email='imzhoutao@gmail.com';
1 row updated (0.00 sec)
```

# 3. 评论系统

目前可用的评论系统，貌似只有disqus，网易云跟帖，但是disqus被墙，所以只能使用网易云跟帖。

编辑`ghost/content/themes/casper/post.hbs`文件，在`</footer>`和`</article>`之间增加云跟帖的**通用代码**
```
<div id="cloud-tie-wrapper" class="cloud-tie-wrapper"></div>
<script>
  var cloudTieConfig = {
    url: document.location.href,
    sourceId: "{{id}}",
    productKey: "dfasfasdfasdfasdfadsfadsf", //使用你自己的productKey
    target: "cloud-tie-wrapper"
  };
</script>
<script src="https://img1.cache.netease.com/f2e/tie/yun/sdk/loader.js"></script>
```

# 4. 中文字体

在参考了这篇文章[^1]后，发现修改了字体后，果然汉字看起来舒服多了，感谢分享。打开 `ghost/content/themes/casper/assets/css/screen.css`文件，修改body定义的**font-family**，修改后的结果
```
body {
    height: 100%;
    max-height: 100%;
    font-family: "Open Sans", "Helvetica Nenu", Helvetica, Arial, "Liberation Sans", "Hiragino Sans GB", "Source Han Sans CN Normal", "Microsoft YaHei", sa
ns-serif;
    letter-spacing: 0.01rem;
    font-size: 1.8rem;
    line-height: 1.75em;
    color: #3A4145;
    -webkit-font-feature-settings: 'kern' 1;
    -moz-font-feature-settings: 'kern' 1;
    -o-font-feature-settings: 'kern' 1;
    text-rendering: geometricPrecision;
}
```

# 5. 时间格式

这个就看自己了，是否喜欢ghost默认的时间格式，如果不喜欢，可以根据[文档](http://momentjs.com/docs/)修改格式化字符串输出你想要的结果样式。

主要修改`ghost/content/theme/casper/post.hbs` 文件和`ghost/content/themes/casper/partials/loop.hbs` 文件.

# 6. 云存储

目前很多站长都是喜欢把图片啊需要分享的文件啊，都保存在云存储，可以方便的挂图，而且即便更换VPS服务商，也不会有头疼的数据迁移问题。目前国内可用的比较多，我这里就说一下七牛云和又拍云吧。两者之间的差异，七牛的https流量是需要付费的，又拍云的https流量包含在免费流量中。如果你的站点开了https，肯定首选友拍云了。

## 6.1 七牛云

### 安装

在`content` 目录新建`storage` 目录，然后git克隆代码，之后`npm install` 。
```
mkdir ghost/content/storage
cd ghost/content/storage
git clone https://github.com/minwe/qn-store.git
cd qn-store
npm install
```

### 配置

修改`config.js` 文件，在production小节`server{}` 前面，增加如下代码
```
    storage: {
      active: 'qn-store',
      'qn-store': {
        accessKey: 'your access key',
        secretKey: 'your secret key',
        bucket: 'your bucket name',
        origin: 'http://xx.xx.xx.glb.clouddn.com',
        // timeout: 3600000, // default rpc timeout: one hour, optional

        // if your app outside of China, please set `uploadURL` to `http://up.qiniug.com/`
        uploadURL: 'http://up.qiniu.com/',

        // file storage key config [optional]
        // if `fileKey` not set, Qiniu will use `SHA1` of file content as key.
        fileKey: {
          // use Qiniu hash as file basename, if set, `safeString` will be ignored
          hashAsBasename: false,
          safeString: true, // use Ghost safaString util to rename filename, e.g. Chinese to Pinyin
          prefix: 'YYYY/MM/', // {String | Function} will be formated by moment.js, using `[]` to escape,
          suffix: '', // {String | Function} string added before file extname.
          extname: true // keep file's extname
        }
      }
    },
```

## 6.2 又拍云

### 安装

在`content` 目录新建`storage` 目录，然后git克隆代码，之后`npm install` 。
```
mkdir ghost/content/storage
cd ghost/content/storage
git clone https://github.com/sanddudu/upyun-ghost-store.git
cd upyun-ghost-store
npm install
```

### 配置

修改`config.js` 文件，在production小节`server{}` 前面，增加如下代码
```
storage: {
  active: 'upyun-ghost-store',
  'upyun-ghost-store': {
    bucket: 'my-bucket', //bucket 名称
    operator: 'somebody', //操作员用户名
    password: 'secret', //操作员密码
    domain: 'http://bucket.b0.upaiyun.com', //空间绑定的域名，必须带 "http(s)://" ，结尾不能有 "/"
    filePath: 'YYYY/MM/', //文件远端保存地址，默认为日期格式，由 moment 解析后填充，
                            您可以修改为 "[您的目录地址]"，开头不能加 "/"，结尾必须加 "/"
                            具体格式参见： [http://momentjs.com/](http://momentjs.com/)
    imgVersion: '' // 自定义版本，需带上分隔符（如 _large ）。使用原图则留空或删除该选项
  }
},
```

# 7. prism语法高亮

打开[prism官网](http://prismjs.com/)，点击**DOWNLOAD**按钮，进入自定义下载页面，**Compression level**选择`Minified version` ，**Themes**选择你喜欢的主题，我选了默认的，**Languages**选择你需要的语法高亮的语言，**Plugins** 主要是选择一些你需要的插件，比如行号啊，行高亮啊，工具栏啊，高亮关键字啊等等，勾选你需要的。最下面有两个按钮，`DOWNLOAD JS` 和 `DOWNLOAD CSS` 把这两个文件下载下来，然后上传到vps。

`prism.js`上传到`ghost/content/themes/casper/assets/js/` 目录

`prism.css` 上传到`ghost/content/themes/casper/assets/css/` 目录

然后修改`default.hbs`文件，在`</head>` 之前加入如下代码

```
<link rel="stylesheet" type="text/css" href="{{asset "css/prism.css"}}" />
```

在`</body>` 之前，加入如下代码

```

<script type="text/javascript" src="{{asset "js/prism.js"}}"></script>

```

OKAY，大功告成，重启应用，以后需要高亮代码的时候，在**```**后面增加语言名称即可，示例如下：

```
​```sql
SELECT * FROM mysql.user;
​```
```

# 8, 待续


[^1]: https://fixatom.com/part-of-ghost-localize/
