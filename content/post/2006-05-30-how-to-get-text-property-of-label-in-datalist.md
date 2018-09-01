---
title: how to get Text property of Label in DataList?
date: 2006-05-30T07:44:47+00:00
layout: post
categories:
  - 我的世界
tags:
  - 'c#'
---

Normally when you want to do that you are doing it from within an event

raised by the DataList. I will assume that is where you are doing this from.

Here is a line of code that will get the Text property of the current Item:

CType(e.Item.FindControl(“lblMyLabel”), Label).Text

If you needed to access this data from a method/function other than one of

the events raised by the DataList, you will need to know the index of the

item the desired Label is in. In this case, use the following:

CType(DataList1.Items(index).FindControl(“lblMyLabel”), Label).Text

Please remember that when accessing data this way you must do it before

calling the databind() method in the case of databound properties (it

doesn&#8217;t look like your ItemTemplate has any databound properties, but I

assume it will), otherwise it will return a value of “”. Good Luck!

—

Nathan Sokalski

njsokalski@hotmail.com

<http://www.nathansokalski.com/>

    “Arvan” <esato> wrote in message
    [news:eiPOPqggGHA.2040@TK2MSFTNGP03.phx.gbl…](news:eiPOPqggGHA.2040@TK2MSFTNGP03.phx.gbl...)
    > hi,all.
    >
    > i placed a datalist named DataList1 and placed a Label named Label1 in
    > item template.
    >
    > how do i get the text of control Label?
    >
    > code:
    >
    ><datalist id="DataList1" repeatcolumns="4" runat="server"></datalist>> RepeatLayout=”Flow” ShowFooter=”False” ShowHeader=”False”>
    ><itemtemplate>
    ><label id="img" runat="server" text="test"></label>> >
    ></itemtemplate>
    >
    ></esato>