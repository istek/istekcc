---
title: FCKeditorWeb在线编辑器说明
date: 2006-05-20T06:02:39+00:00
layout: post
categories:
  - 我的世界
tags:
  - fckeditor
---

现在网络上有很多不一样的web在线编辑器，大多数都是商业程序，要使用更多功能的话得掏钱。比如FreeTextbox，CuteEditor（听说它非常强大）。

我选择了FckEditor，主要它是轻量级的，功能丰富的，安装使用简单的，开源的在线编辑器，而且开源，下载的包里有项目文件，你可以按照自己的需要进行修改代码，然后再次生成组件。而且它支持IE 5.5+ (Windows), Firefox 1.0+, Mozilla 1.3+和Netscape 7+以上浏览器，并且支持ASP，CGI，PHP，ASP.NET，Java，ColdFusion，Python等脚本。

好了，闲话不说，我这里提一下在ASP.NET下一般的安装和使用方法。

1、下载FckEditor 2.3 Beta，它主要包括核心文件。

2、下载FckEditor 2.2 .Net ，包括ASP.NET的DLL文件，并且解压到任意目录。

3、解压FckEditor 2.3 Beta压缩包，将文件夹FCKeditor复制到网站的根目录，这里以ASP.NET 2.0的test项目为例，将其复制到test的根目录，并且在根目录下新建uploads目录用来存放编辑器上传得文件。

4、打开FCKeditor目录下的fckconfig.js文件，将FCKConfig.DefaultLanguage的值改为zh-cn使其的界面语言改变为简体中文，_FileBrowserLanguage和_QuickUploadLanguage的值都改为aspx。可选的修改如下，可以修改编辑器的skin,将FCKConfig.SkinPath = FCKConfig.BasePath + &#8216;skins/default/&#8217; 的default可以该为office2003或者silver。保存修改，关闭文件。

5、打开test项目的web.config文件，在<appsettings>节点下增加<add key="FCKeditor:UserFilesPath" value="/Weblog/uploads"></add>。不知道为什么FCKeditor不支持相对路径，如果设置为“~/uploads”就是用不了，上传得文件地址都变成”<http://localhost/~/uploads/xxxxxx.jpg”这样的了。在><system .web="">节点下增加<pages validaterequest="false"></pages>，要不然当在编辑器里输入字符保存时，会出现“检测到有潜在危险对象的Request.form值”的警告，好了，保存文件。</system></appsettings>

6、在项目中引用刚才解压的FCKeditor.NET压缩包里的FredCK.FCKeditorV2.dll文件。具体位置是 FCKeditor.Net_2.2/bin/release/FredCK.FCKeditorV2.dll

7、打开test项目的default.aspx页面，在

下面加入以下代码

然后就可以在default.aspx页面使用这个控件了：

```
    <fckeditorv2 :fckeditor="" basepath="~/FCKeditor/" enablesourcexhtml="true" enablexhtml="true" height="500px" id="FCKeditor" runat="server" width="580px"></fckeditorv2>
```

8、如何取得编辑器中的文本。该控件有个属性是value，它就是获得编辑器中的文本的。

9、FCKeditor控件的属性和事件。

具有的属性列表如下：
```
    AutoDetectLanguage
    BaseHref
    BasePath
    ContentLangDirection
    CustomConfigurationsPath
    Debug
    DefaultLanguage
    EditorAreaCSS
    EnableSourceXHTML
    EnableViewState
    EnableXHTML
    FillEmptyBlocks
    FontColors
    FontFormats
    FontNames
    FontSizes
    ForcePasteAsPlainText
    ForceSimpleAmpersand
    FormatIndentator
    FormatOutput
    FormatSource
    FullPage
    GeckoUseSPAN
    Height
    ID
    ImageBrowserURL
    LinkBrowserURL
    PluginsPath
    runat
    SkinPath
    StartupFocus
    StylesXMLPath
    TabSpaces
    ToolbarCanCollapse
    ToolbarSet
    ToolbarStartExpanded
    UseBROnCarriageReturn
    Value
    Visible
    Width
```

具体的事件列表如下：
```
    OnDataBinding
    OnDisposed
    OnInit
    OnLoad
    OnPreRender
    OnUnload
 ```

10、其他

官方网站的support上提到，如果使用asp.net 2.0和theme，那么需要打开editorfilemanageruploadaspxupload.aspx和editorfilemanagerrowserdefaultconnectorsaspxconnector.aspx文件，并且在第一行中加入Theme=”” 。比如

如果还有什么不知道，可以访问 <http://www.fckeditor.net/>

编辑器下载:

![下载文件](images/download.gif)[点击下载FCKeditor v2.3 Beta](attachments/month_0605/5200651914122.zip)

![下载文件](images/download.gif)[点击下载FCKeditor.Net_2.2](attachments/month_0605/3200651914156.zip)