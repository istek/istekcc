---
title: Preparation of Developping Blog
date: 2007-06-26T16:16:20+00:00
layout: post
categories:
  - 随笔
---

Under this constructions, I will code a program by using C#. And now I just prepare some dirty work. I don&#8217;t know when it will be released. Infomation will be added once I have a good idea.

**Ⅰ_*</strong>. Functions_*</p>

1. User can log in;.
2. Post a log or draft;
3. Search by Log Ttile and Log Content;
4. Calendar. Regroup all logs by date. It seems that be a kind of archive;
5. Visitors can post comment and trackback logs and be logged their’s ip;
6. Blog can be managed including logs, comments, trackbacks and so on;
7. Security some log;
8. RSS 2.0
9. Adding………….
<!--more-->

**Ⅱ_*</strong>. Database_*</p>

1. Table User includes 5 fields: uid(Primary Key), uname, upasswd, uweb, uemail. The uname and upasswd, uweb and uemail are format of text.and the max length is 50.
2. Table Category includes CateId(Primary Key), CateTtitle and CateDesc. The last 2 fields is text and length of 50.
3. Table Link includes 4 fields: LnId(Primary Key), LnName, LnUrl and LnComm.The table is storing favirate links. And it could be set-up from background.
4. Table Log is storing all logs written by author. And There’re LogId, LogName, LogDate, LogText, LogSummary, LogWeather, LogViews, LogComments, IsDraft(boolean), IsSecret(boolean) in Log.and LogId is Primary Key.
5. Table Comment is storing all comments. And it include ComtId(Primary Key), ComtName, ComtText, ComtDate, ComtEmail, ComtUrl and LogId.
6. Table Trackback is having all trackbacks. And it will contain every log’s trackback. There are TbId, Tburl, TbTitle, TbKey and LogId. TbKey is used for stopping spam.and it will change when another website trackbacks the log.

**Ⅲ_*</strong>.Work Progress_*</p>

1. User must log in. And then the user can post a log or draft.
2. Log can’t be empty in LogName,LogText. It’s nessesary to enter those fields.
3. And use caledar for regroup all log by date.
4. Visitors can search all logs except secret logs by log title or log content. It need a field to make it be secret.
5. Visitors can post a comment. Or leave a message to blogger.
6. Visitors can trackback all logs.(It depends on tbkey. Because TbKey is changed every time.).
7. All settings will store in web.config file. For example: Blog Name, Sub Title and so on.


**Ⅳ_*</strong>.Security & Others_*</p>

  1. Protect web.config file.

aspnet_regiis -pe “connectionStrings” -app “/SampleApplication”

  1. Use oledbParameters.
  2. Verify Code.

