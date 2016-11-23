---
title: IOS 使用域名进行Socket连接
id: 289
categories:
  - Cocos2D-X
date: 2012-09-25 10:57:05
tags:
---

项目中用到了socket连接，由于我使用的boost，而boost的建立连接是只能传IP地址的，而我服务器地址是域名，所以就要将域名转到IP。

&nbsp;

看了下代码，其实也挺简单的。

[cpp]

//SERVER_ADDRESS  Server Domain

hostent* host_entry = gethostbyname(SERVER_ADDRESS);

char IPStr[64] = {0};

if(host_entry !=0){

sprintf(IPStr, &quot;%d.%d.%d.%d&quot;,(host_entry-&gt;h_addr_list[0][0]&amp;0x00ff),

(host_entry-&gt;h_addr_list[0][1]&amp;0x00ff),

(host_entry-&gt;h_addr_list[0][2]&amp;0x00ff),

(host_entry-&gt;h_addr_list[0][3]&amp;0x00ff));

}

[/cpp]

&nbsp;

这样就可以了，gethostbyname(),是系统自己的库函数，可以解析域名到结构体hostent，然后自己解析下结构体就可以了。