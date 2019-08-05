---
title: 关于数据绑定的问题
date: 2006-05-27T02:48:25+00:00
layout: post
categories:
  - .Net
---

最近写日志的时间太少了，真的对不起自己了。主要是忙着单位上的事情，还有就是学习.NET。下面就把我最近遇到的一些问题，写下来。

一。GridView的数据绑定问题。

在ASP.NET页面，如何绑定数据字段是一个可大可小的问题。为什么这么说那，你可以直接添加datasource控件，来绑定数据，这是最简单的。其次就是在代码页面添加代码后，绑定到控件上。

当我在编Weblog的程序时，我刚开始就是使用datasource控件完成这一切的，但是我觉得有些时候是datasource无法完成的，比如我写了一个类专门用来处理weblog的一些操作。比如添加日志，显示日志等等。这个时候，你就不能很轻松的绑定到数据控件上。

我在这里就是写写我的方法，如果其他人有比我更好的方法，请告诉我啊！谢谢。

首先，我们需要在内容页面添加GridView控件，增加一个模板列，然后将AutoGenerateColums设为False,这样就不会自动生成列字段了。然后我们编辑ItemTemplate，按照你想的样式进行布局，排列。然后，切换到源码视图，比如在itemtemplate模板里面增加了一个HyperLink控件用来显示作者，并且连接指向作者的email。现在在源码视图里面增加绑定的代码。

```
    <asp :hyperlink="" eval="" id="articlename" navigateurl="'<%#" runat="server">&#8216; ></asp>
```

好，现在heyperlink控件就绑定了2个数据字段，一个是email，还有一个是Username。Eval有2个可选参数，第一个是要绑定的字段，第二个是格式表达式。具体的关于Eval的说明，大家可以查阅MSDN。

现在内容页面已经做完了。只要在代码页面加入 GridView1.datasource = DataSet1;GridView1.databind();就可以了。这样数据绑定就在代码页面完成了，我可能说的不清楚，大家动手做一做就懂了。：）

2。另外一种数据绑定。来自MSDN。

请注意最后的那个Label控件的Text属性和SubmitBtn_Click事件。两者一结合，显然说明了另外一种数据绑定办法。

3。字符串的操作。

其实我说的字符串操作，主要是指连接多个字符串，因为我在写SQL语句的时候，每次sql语句都特长，就想分成几行写。

  第一种连接方法就是 ＋＝ 操作符。
```
  <textarea class="code" cols="50" rows="10">string a = ” this is”; <br /> a += ” a test”;</textarea>
```
输出以后，完整的a就是“this is a test”。

第二种连接方法是 ＋ 运算符。

第三种办法就是 StringBuilder.
```
Using System.Text;

StringBuilder sb = new StringBuilder();

sb.Append(“This is “);
sb.Append(“a test”);
string result = sb.toString();
```
就输出完整的result字符串“this is a test”。

期望这些给刚学习C＃的新手一个tip吧。呵呵。
