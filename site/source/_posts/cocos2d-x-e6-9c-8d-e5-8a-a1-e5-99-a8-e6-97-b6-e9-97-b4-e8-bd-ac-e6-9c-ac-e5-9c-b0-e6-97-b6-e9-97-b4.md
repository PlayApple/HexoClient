---
title: cocos2d-x 服务器时间转本地时间
tags:
  - cocos2d-x
id: 322
categories:
  - Cocos2D-X
date: 2012-10-19 07:13:12
---

做网络游戏时，服务器的时间在本地显示的时候要转成本地时间，可以利用系统函数转化。

[cpp]

long long int timeSec = &quot;1353735416&quot;;//假如这是服务器的时间
char str[64] = {0};
time_t timep = timeSec;
sprintf(str, &quot;%s&quot;,asctime(gmtime(&amp;timep)));

[/cpp]

&nbsp;

str是 星期+日期+时间

&nbsp;

如果希望自己定义格式，用下面的办法也可以。

[cpp]
long long int timeSec = &quot;1353735416&quot;;//假如这是服务器的时间
char str[64] = {0};
time_t timep = timeSec;
tm* timeStuct = gmtime(&amp;timep);
sprintf(str, &quot;%02d/%02d/%02d %02d:%02d&quot;,
timeStuct-&gt;tm_year + 1900,
timeStuct-&gt;tm_mon + 1,
timeStuct-&gt;tm_mday,
timeStuct-&gt;tm_hour,
timeStuct-&gt;tm_min);

[/cpp]

&nbsp;