---
title: How to make Apache run ASP.NET / ASP.NET 2.0 under windows
date: 2008-01-18T18:56:46+00:00
layout: post
categories:
  - .Net
---
Don't ask me why… but i've been asked to make Apache run ASP.NET.

**IT Worked ! _*

*_Even worked with ASP.NET 2.0 Site !**

Following are the instruction to make Asp.Net work under apache:

* Install **Apache 2.0.54**
* Install **Mod_AspDotNet**
* Add at the end of `C:\Program Files\Apache Group\Apache2\conf\httpd.conf` the following lines
<!--more-->

```
# asp.net

LoadModule aspdotnet_module "modules/mod_aspdotnet.so"
AddHandler asp.net asax ascx ashx asmx aspx axd config cs csproj licx rem resources resx soap vb vbproj vsdisco webinfo

<IfModule mod_aspdotnet.cpp>
# Mount the ASP.NET /asp application
AspNetMount /SampleASP "c:/SampleASP"
#/SampleASP is the alias name for asp.net to execute
#"c:/SampleASP" is the actual execution of files/folders in that location
# Map all requests for /asp to the application files
Alias /SampleASP "c:/SampleASP"

#maps /SampleASP request to "c:/SampleASP"
#now to get to the /SampleASP type [http://localhost/SampleASP]
#It'll redirect http://localhost/SampleASP to "c:/SampleASP"
# Allow asp.net scripts to be executed in the /SampleASP example

<Directory "c:/SampleASP">
Options FollowSymlinks ExecCGI
Order allow,deny
Allow from all
DirectoryIndex index.htm index.aspx
#default the index page to .htm and .aspx
</Directory>

# For all virtual ASP.NET webs, we need the aspnet_client files
# to serve the client-side helper scripts.
AliasMatch /aspnet_client/system_web/(\d+)_(\d+)_(\d+)_(\d+)/(.*) "C:/Windows/Microsoft.NET/Framework/v$1.$2.$3/ASP.NETClientFiles/$4"

<Directory "C:/Windows/Microsoft.NET/Framework/v*/ASP.NETClientFiles">
Options FollowSymlinks
Order allow,deny
Allow from all
</Directory>

</IfModule>
# asp.net
```

* Create a directory `c:\SampleASP` and insert in it the index.aspx
* Restart apache server :
```
Start->Apache HTTP Server 2.0.54 ->
```
Control Apache Server -> Restart
* Open Explorer and navigate to `http://localhost/SampleASP/index.aspx`

If everything worked fine you should get a nice asp.net page working.

**— index.aspx —**
```
<%@ Page Language="VB" %>
<link rel="stylesheet"href="intro.css">
<center>
<form action="index.aspx" method="post">
<h3> Name: <input id="Name" type=text>
Category: <select id="Category" size=1>
<option>One</option>
<option>Two</option>
<option>Three</option>
</select>
</h3>
<input type=submit value="Lookup">
<p>
<% Dim I As Integer
For I = 0 to 7 %>
<font size="<%=I%>"> Sample ASP.NET TEST</font> <br>
<% Next %>
</form>
</center>
```
