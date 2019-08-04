---
title: '使用C#开发一个简单的ASP.net程序留言本'
date: 2006-07-01T07:35:38+00:00
layout: post
categories:
  - .Net
tags:
  - c#
---

这个留言本使用的数据库是access 2003，当然你也可以以sql server 2k来做。程序只有3个文件，default.aspx主要用来展示留言及书写留言，checkcode.aspx是验证码的代码文件，admin.aspx 用来管理留言部分，我写的很简单，就是一个功能，删除。

sql部分，为了避免sql注入的威胁，我使用了oledbparameters来引入参数。现在我把代码贴出来，供那些正在学习c#，有志于学习c#的朋友参考一下，代码如果有什么漏洞，大家可以提出来。总之，就是一个目的，共同学习。

本留言本的在线实例: <http://et.bestzhou.org/>

以下代码仅供学习试验,请勿使用于生产环境.

default.aspx 文件代码
```
    <%@ page language=”c#” autoeventwireup=”true”  codefile=”default.aspx.cs” inherits=”_default” %>
    <!doctype html public “-//w3c//dtd xhtml 1.0 transitional//en” “http://www.w3.org/tr/xhtml1/dtd/xhtml1-transitional.dtd”>
    <html xmlns=”http://www.w3.org/1999/xhtml” >
    <head runat=”server”>
        <title>atlansing guestbook v1.0 beta</title>
        <link href=”style.css” rel=”stylesheet” type=”text/css” />


        <form id=”form1″ runat=”server”>
        <div id=”pagediv”>
        <div id=”msglist” runat=”server”>
            <asp:gridview id=”gridview1″ autogeneratecolumns=”false” runat=”server”  width=”70%” allowpaging=”true” borderstyle=”none” borderwidth=”0px” cellpadding=”0″ showheader=”false” gridlines=”none”>
                <columns>
                    <asp:templatefield showheader=”false”>
                        <itemtemplate>
                       <div id=”msgdiv”>
                            <div class=”author”><asp:image id=”image1″ runat=”server” imagealign=”absmiddle” imageurl=”~/images/icon_quote.gif” /><strong><asp:hyperlink id=”author” runat=”server”  text=‘‘<%# eval(“author”) %>‘‘ navigateurl=‘‘<%# eval(“email”,”mailto:{0}”) %>‘‘></asp:hyperlink></strong>
                                [ <asp:label id=”date” runat=”server” text=‘‘<%# eval(“date”) %>‘‘></asp:label> | <asp:label id=”ip” runat=”server” text=‘‘<%# eval(“ip”) %>‘‘></asp:label> ]</div>
                               <div id=”msgcontent”><asp:label id=”msg” runat=”server” text=‘‘<%# eval(“msg”) %>‘‘></asp:label> </div> 
                        </div><br />
                        </itemtemplate>
                    </asp:templatefield>
                </columns>
            </asp:gridview>
           </div> 
           <div>
            <table width=”80%” border=”0″ cellpadding=”0″ style=”text-align: left”>
                <tr>
                    <td style=”text-align: right; width: 170px;”>昵称:</td>
                    <td style=”width: 292px”>
                        <asp:textbox id=”author” runat=”server”></asp:textbox></td>
                     <td style=”width: 130px”>
                         <asp:requiredfieldvalidator id=”requiredfieldvalidator1″ runat=”server” controltovalidate=”author” errormessage=”*必须填写”></asp:requiredfieldvalidator></td> 
                </tr>
                <tr>
                    <td style=”text-align: right; width: 170px;”>验证码:</td>
                    <td style=”width: 292px”>
                        <asp:textbox id=”checkcode” runat=”server”></asp:textbox>
                           <asp:image id=”image2″ runat=”server” imageurl=”~/checkcode.aspx” /></td>
                     <td style=”width: 130px”>
                         <asp:requiredfieldvalidator id=”requiredfieldvalidator3″ runat=”server” controltovalidate=”author” errormessage=”*必须填写”></asp:requiredfieldvalidator></td> 
                </tr> 
                <tr>
                    <td style=”text-align: right; width: 170px;”>email:</td>
                    <td style=”width: 292px; height: 20px”>
                        <asp:textbox id=”email” runat=”server”></asp:textbox></td>
    &nbsp
     ;                  <td id=”tablecell5″ runat=”server” style=”height: 20px; width: 130px;”>
                           </td>  
                </tr>
                <tr>
                    <td style=”text-align: right; width: 170px;”>website:</td>
                    <td style=”width: 292px”>
                        <asp:textbox id=”website” runat=”server”></asp:textbox></td>
                       <td id=”tablecell3″ runat=”server” style=”width: 130px”></td>  
                </tr>
                           <tr id=”tablerow1″ runat=”server”>
                    <td id=”tablecell1″ runat=”server” style=”height: 80px; text-align: right; width: 170px;”>留言内容:</td>
                    <td id=”tablecell2″ runat=”server” style=”width: 292px; height: 80px”>
                        <asp:textbox id=”msg” runat=”server” textmode=”multiline” height=”70px” width=”283px”></asp:textbox></td>
                       <td id=”tablecell4″ runat=”server” style=”height: 80px; width: 130px;”>
                           <asp:requiredfieldvalidator id=”requiredfieldvalidator2″ runat=”server” errormessage=”*必须填写” controltovalidate=”msg”></asp:requiredfieldvalidator></td>  
                </tr> 
               <tr>
                    <td style=”text-align: right; width: 170px;”></td>
                    <td style=”width: 292px”>
                        <asp:button id=”savebtn” runat=”server” text=”发表留言” onclick=”savebtn_click” />   <asp:button id=”cancelbtn” runat=”server” text=”重写” />
                        <asp:label id=”resultlbl” runat=”server” font-bold=”true” forecolor=”red”></asp:label>
                    </td>
               </tr> 
            </table>
               <br />
               <br />
               powered by <a href=”http://www.bestzhou.org/” title=”马上访问”>atlansing guestbook 1.0 beta</a><br />
               copy © 2006-2007, bestzhou.org</div>
        </div>
        </form>
```

default.aspx.cs 源代码文件
```
    using system;
    using system.data;
    using system.data.sqlclient;
    using system.data.oledb;
    using system.configuration;
    using system.web;
    using system.web.security;
    using system.web.ui;
    using system.web.ui.webcontrols;
    using system.web.ui.webcontrols.webparts;
    using system.web.ui.htmlcontrols;
    using system.security.cryptography;</textarea>

    public partial class _default : system.web.ui.page
        {
            public static string filename = “~//app_data//guestbook.mdb”;
        public static string connstring = “provider=microsoft.jet.oledb.4.0; data source=” + system.web.httpcontext.current.server.mappath(filename);
            protected void page_load(object sender, eventargs e)
            {
                oledbconnection odbconn = new oledbconnection(connstring);
                oledbdataadapter dbada = new oledbdataadapter(“select * from [guestbook] order by [date] desc”, odbconn);
                dataset ds = new dataset();
                dbada.fill(ds);
                gridview1.datasource = ds;
                gridview1.databind();
                if (gridview1.rows.count == 0)
                {
                    gridview1.visible = false;
                    msglist.innerhtml = “<div style=”text-align:center”><strong>目前尚无新的留言</strong></div>”;
                }
                session.add(“ips”, request.userhostaddress);
                if (ispostback != false)
                {
                    gridview1.datasource = ds;
             
            gridview1.databind();
                }
            }

            protected void savebtn_click(object sender, eventargs e)
            {
                savebtn.enabled = false;
                if (string.compare(session[“checkcode”].tostring(), checkcode.text,true) == 0)
                {
                    string myauthor = server.htmlencode(author.text);
                    //string mymsg = server.htmlencode(msg.text);
                    string mymsg = httputility.htmlencode(msg.text);
                    mymsg=mymsg.replace(“<b>”, “<b>”);
                    mymsg=mymsg.replace(“</b>”, “</b>”);
                    mymsg=mymsg.replace(“<i>”, “<i>”);
                    mymsg=mymsg.replace(“</i>”, “</i>”);
                    string myweb = server.htmlencode(website.text);
                    string myemail = server.htmlencode(email.text);
                    oledbconnection odbconn = new oledbconnection(connstring);
                    odbconn.open();
                    oledbdataadapter oleda = new oledbdataadapter(“select * from [guestbook] order by [date] desc”, odbconn);
                    try
                    {
                        oledbcommand olecmd = new oledbcommand(“insert into [guestbook] (author,msg,email,website,ip) values (?,?,?,?,?)”, odbconn);
                        oledbparameter p_author = olecmd.parameters.add(“@author”, oledbtype.varwchar, 12);
                        p_author.value = myauthor;
                        oledbparameter p_msg = olecmd.parameters.add(“@msg”, oledbtype.varwchar);
                        p_msg.value = mymsg;
                        oledbparameter p_email = olecmd.parameters.add(“@email”, oledbtype.varwchar);
                        p_email.value = myemail;
                        oledbparameter p_web = olecmd.parameters.add(“@web”, oledbtype.varwchar);
                        p_web.value = myweb;
                        oledbparameter p_ip = olecmd.parameters.add(“@ip”, oledbtype.varwchar);
                        p_ip.value = session[“ips”].tostring();
                        int i = olecmd.executenonquery();
                        if (i != 0)
                        {
                            author.text = “”;
                            email.text = “”;
                            website.text = “”;
                            msg.text = “”;
                            checkcode.text = “”;
                            savebtn.enabled = true;
                            resultlbl.text = “保存留言成功”;
                        }
                    }
                    catch(oledbexception ex)
                    {
                        resultlbl.text = ex.message;
                    }
                    catch(exception ex)
                    {
                        resultlbl.text=ex.message;
                    }
              &
     nbsp;    
                    dataset ds = new dataset();
                    oleda.fill(ds);
                    odbconn.close();
                    gridview1.datasource = ds;
                    gridview1.databind();
                }
                else
                {
                    resultlbl.text = “验证码错误”;
                    savebtn.enabled = true;
                    return;            
                }
            }
        }
```

checkcode.aspx 页面代码:
```
    <%@ page language=”c#” autoeventwireup=”true” codefile=”checkcode.aspx.cs” inherits=”checkcode” %>
    <!doctype html public “-//w3c//dtd xhtml 1.0 transitional//en” “http://www.w3.org/tr/xhtml1/dtd/xhtml1-transitional.dtd”></textarea>

    <html xmlns=”http://www.w3.org/1999/xhtml” >
    <head runat=”server”>
        <title>无标题页</title>
    <!– 点击后退按钮,刷新验证码 –>
    <% response.buffer = true ;%>
    <% response.expiresabsolute = datetime.now.addseconds(-1);%>
    <% response.expires = 0 ;%>
    <% response.cachecontrol = “no-cache” ; %>


        <form id=”form1″ runat=”server”>
        <div>
        
        </div>
        </form>
```

checkcode.aspx 源文件代码:
```
    using system;
    using system.data;
    using system.configuration;
    using system.collections;
    using system.web;
    using system.web.security;
    using system.web.ui;
    using system.web.ui.webcontrols;
    using system.web.ui.webcontrols.webparts;
    using system.web.ui.htmlcontrols;
    using system.drawing;</textarea>

    public partial class checkcode : system.web.ui.page
    {
        //验证码由生成彩色验证码和zhongfeng, http://blog.csdn.net/sw515 的验证码综合而成.
        //感谢他们的劳动.
        protected void page_load(object sender, eventargs e)
        {
            createimage(generatecheckcode());
        }

        private string generatecheckcode()
        {
            //生成checkcode
            int number;
            char code;
            string checkcode = string.empty;

            system.random random = new random();

            for (int i = 0; i < 5; i++)
            {
                number = random.next();

                if (number % 2 == 0)
                    code = (char)(‘‘0‘‘ + (char)(number % 10));
                else
                    code = (char)(‘‘a‘‘ + (char)(number % 26));

                checkcode += code.tostring();
            }

            //response.cookies.add(new httpcookie(“checkcode”, checkcode));
            session.add(“checkcode”, checkcode);
            return checkcode;
        }

        private void createimage(string checkcode)
        {
            if (checkcode == null || checkcode.trim() == string.empty)
                return;
            int iwidth = (int)(checkcode.length * 15);
            system.drawing.bitmap image = new system.drawing.bitmap(iwidth, 25);
            graphics g = graphics.fromimage(image);
            g.clear(color.white);
            //定义颜色
            color[] c = { color.black, color.red, color.darkblue, color.green, color.orange, color.brown, color.darkcyan, color.purple };
            //定义字体            
            string[] font = {“verdana”,”microsoft sans serif”,”comic sans ms”,”arial”,”宋体”};
            random rand = new random();
            //随机输出噪点
            for (int i = 0; i < 100; i++)
            {
                int x = rand.next(image.width);
                int y = rand.next(image.height);
                g.drawrectangle(new pen(color.lightgray, 0), x, y, 1, 1);
            }

    //输出不同字体和颜色的验证码字符
            for (int i = 0; i < checkcode.length; i++)
            {
                int cindex = rand.next(7);
                int findex = rand.next(5);

                font f = new system.drawing.font(font[findex], 10, system.drawing.fontstyle.bold);
                brush b = new system.drawing.solidbrush(c[cindex]);
                int ii = 4;
                if ((i + 1) % 2 == 0)
                {
                    ii = 2;
                }
                g.drawstring(checkcode.substring(i, 1), f, b, 3 + (i * 12), ii);
            }
            //画一个边框
            g.drawrectangle(new pen(color.black, 0), 0, 0, image.width – 1, image.height – 7);

            //输出到浏览器
            system.io.memorystream ms = new system.io.memorystream();
            image.save(ms, system.drawing.imaging.imageformat.jpeg);
            response.clearcontent();
            response.contenttype = “image/jpeg”;
            response.binarywrite(ms.toarray());
            g.dispose();
            image.dispose();
        }
    }
```

admin.aspx 页面代码:
```
    <%@ page language=”c#” autoeventwireup=”true” codefile=”admin.aspx.cs” inherits=”admin” %>
    <!doctype html public “-//w3c//dtd xhtml 1.0 transitional//en” “http://www.w3.org/tr/xhtml1/dtd/xhtml1-transitional.dtd”>
    <html xmlns=”http://www.w3.org/1999/xhtml” >
    <head runat=”server”>
        <title>留言本管理-arvan留言本</title>


        <form id=”form1″ runat=”server”>
        <div>
            <asp:multiview id=”multiview1″ runat=”server” activeviewindex=”0″>
                <asp:view id=”view1″ runat=”server”>
                    <asp:textbox id=”passwdtxt” runat=”server” textmode=”password”></asp:textbox>
                    <asp:requiredfieldvalidator id=”requiredfieldvalidator1″ runat=”server” controltovalidate=”passwdtxt”
                        display=”dynamic” errormessage=”*请输入密码” setfocusonerror=”true”></asp:requiredfieldvalidator><br />
                    <asp:button id=”loginbtn” runat=”server” onclick=”loginbtn_click” text=”登录” />
                    <asp:label id=”resultlbl” runat=”server”></asp:label></asp:view>
                <asp:view id=”view2″ runat=”server”>
                    <asp:gridview id=”msggridview” runat=”server” allowpaging=”true” autogeneratecolumns=”false”
                        datakeynames=”id” datasourceid=”accessdatasource1″ horizontalalign=”center” width=”80%”>
                        <columns>
                            <asp:boundfield datafield=”id” headertext=”编号” insertvisible=”false” readonly=”true”
                                sortexpression=”id”>
                                <itemstyle horizontalalign=”center” />
                            </asp:boundfield>
                            <asp:boundfield datafield=”author” headertext=”作者” sortexpression=”author”>
                                <itemstyle horizontalalign=”center” />
                            </asp:boundfield>
                            <asp:boundfield datafield=”msg” headertext=”留言内容” sortexpression=”msg” />
                            <asp:boundfield datafield=”date” headertext=”日期” sortexpression=”date”>
                                <itemstyle horizontalalign=”center” />
                            </asp:boundfield>
                            <asp:boundfield datafield=”ip” headertext=”作者ip” sortexpression=”ip”>
                <itemstyle horizontalalign=”center” />
                            </asp:boundfield>
                            <asp:commandfield buttontype=”button” showdeletebutton=”true”>
                                <itemstyle horizontalalign=”center” />
                            </asp:commandfield>
                        </columns>
                    </asp:gridview>
                    <asp:accessdatasource id=”accessdatasource1″ runat=”server” conflictdetection=”compareallvalues”
                        datafile=”~/app_data/guestbook.mdb” deletecommand=”delete from [guestbook] where [id] = ? and [author] = ? and [msg] = ? and [date] = ? and [ip] = ?”
                        insertcommand=”insert into [guestbook] ([id], [author], [msg], [date], [ip]) values (?, ?, ?, ?, ?)”
                        oldvaluesparameterformatstring=”original_{0}” selectcommand=”select [id], [author], [msg], [date], [ip] from [guestbook] order by [date] desc”
                        updatecommand=”update [guestbook] set [author] = ?, [msg] = ?, [date] = ?, [ip] = ? where [id] = ? and [author] = ? and [msg] = ? and [date] = ? and [ip] = ?”>
                        <deleteparameters>
                            <asp:parameter name=”original_id” type=”int32″ />
                            <asp:parameter name=”original_author” type=”string” />
                            <asp:parameter name=”original_msg” type=”string” />
                            <asp:parameter name=”original_date” type=”datetime” />
                            <asp:parameter name=”original_ip” type=”string” />
                        </deleteparameters>
                        <updateparameters>
                            <asp:parameter name=”author” type=”string” />
                            <asp:parameter name=”msg” type=”string” />
                            <asp:parameter name=”date” type=”datetime” />
                            <asp:parameter name=”ip” type=”string” />
                            <asp:parameter name=”original_id” type=”int32″ />
                            <asp:parameter name=”original_author” type=”string” />
                            <asp:parameter name=”original_msg” type=”string” />
                            <asp:parameter name=”original_date” type=”datetime” />
                            <asp:parameter name=”original_ip” type=”string” />
                        </updateparameters>
                        <insertparameters>
                            <asp:parameter name=”id” type=”int32″ />
                            <asp:parameter name=”author” type=”string” />
                            <asp:parameter name=”msg” type=”string” />
                            <asp:parameter name=”date” type=”datetime” />
                            <asp:parameter name=”ip” type=”string” />
                        </insertparameters>
                    </asp:accessdatasource>
                  <asp:button id=”logoutbtn” runat=”server” text=”退出” onclick=”logoutbtn_click” /> 
                </asp:view>
            </asp:multiview></div>
        </form>
```

admin.aspx 源代码页面
```
    using system;
    using system.data;
    using system.configuration;
    using system.collections;
    using system.web;
    using system.web.security;
    using system.web.ui;
    using system.web.ui.webcontrols;
    using system.web.ui.webcontrols.webparts;
    using system.web.ui.htmlcontrols;</textarea>

    public partial class admin : system.web.ui.page
    {
        protected void page_load(object sender, eventargs e)
        {

        }
        protected void loginbtn_click(object sender, eventargs e)
        {
            if (passwdtxt.text == “password”)
            {
                multiview1.activeviewindex = 1;
            }
            else
            {
                resultlbl.text = “密码不正确请重试”;
            }
        }
        protected void logoutbtn_click(object sender, eventargs e)
        {
            multiview1.activeviewindex = 0;
        }
    }
```

css文件代码:
```
    body
    {
     font-family:宋体 georgia;
     font-size: 12px;
     text-align:center;
    }
    #pagediv
    {
     margin-left:auto;
     margin-right:auto;
    }
    #msglist
    {
     margin-left:auto;
     margin-right:auto;
    }
    #msgdiv
    {
     background-color: #f8f8ff;
     border-bottom: #ff6600 1px dashed;
     border-top: #ff6600 1px dashed;
    }
    #msgcontent
    {
     font-size: 14px;
     margin: 10px 10px 10px 10px;
     padding: 10px 10px 10px 10px;
     font-family: georgia, 宋体;
     text-align: left;
    }
    /*结构完成*/
    .author
    { 
     font-size: 14px; 
     text-align: left;
     font-family: georgia, 宋体;
     margin-top:5px;
    }
    a, a:link, a:visited, a:active
    {
     font-size: 14px;
     text-decoration: none;
     color: #9400d3;
    }
    a:hover
    {
     color: #ff6600;
     border-bottom: #808080 1px dashed;
    }
    img
    {
     vertical-align: middle;
     text-align: center;
    }
```