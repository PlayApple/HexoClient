---
title: cocos2d-x 开发网络游戏（http post&socket）
tags:
  - post
  - socket
id: 274
categories:
  - Cocos2D-X
date: 2012-07-20 01:32:27
---

最近开发一个手机版的多人在线游戏，和服务器通讯要用到socket长连接和http短连接。刚好这几天有不少人问我这方面的知识。我今天就给大家讲下吧。

&nbsp;

大家应该知道在cocos2d-x上面使用socket一般有BSDSocket，http一般用curl，但实际用的过程中还是有很多问题。之前也有很多人写过这方面的教程，我就不重复了，我说下我自己在项目中使用的。

&nbsp;

<span style="color: #ff0000;">Socket我使用boost，http请求使用libhttp。</span>

&nbsp;

socket比较麻烦，先说libhttp吧。

&nbsp;

一、libhttp的使用方法：

1、首先去网上下载这三个资源文件：curl、jsoncpp、libhttp。

2、下载好后，将他们加到自己的工程libs目录下。

[![](http://www.cocos2dev.com/wp-content/uploads/2012/07/1.png "1")](http://www.cocos2dev.com/wp-content/uploads/2012/07/1.png)

http请求：（这是异步请求）

里面设计到的函数在这几个头文件中

#include "json.h"

#include "Http.h"

&nbsp;

[cpp]

Json::Value BYGameApiRequest::gameLogin(const std::string &amp;name,const std::string &amp;psw){

RequestParam params;

// params就是存放需要post到服务器的数据

// params.addParam(&quot;name&quot;, name);

// params.addParam(&quot;psw&quot;,psw);

Response response;

m_request-&gt;syncRequest(BY_GAME_API_URL+BYMethod::GAME_LOGIN+&quot;?userId=&quot;+name, Request::Post, params, &amp;response);

Json::Value root=NULL;

Json::Reader reader;

// cout &lt;&lt; response.getRawData();

string res=string(response.getRawData(),response.m_rawDataSize);

reader.parse(res.c_str(), root, false);

return root;//这就是服务器返回的json数据格式。

}

[/cpp]

上面的这个方法，就可以实现和服务器的post请求，这个函数一般放在什么地方好呢？

我的建议是，在游戏工程中建立一个单例类GameAPIRequest，这个单例类就负责请求所有的api数据，一般请求的api是有自己公司定义的格式的，所以所有的函数、参数，我建议建立一个Config和Method的配置类，专门宏定义格式给GameAPIRequest使用。这样服务器修改了格式什么的，只需要在配置类中修改就可以了。

&nbsp;

二、socket使用方法：

添加boostframework库到libs，这个需要自己将boost打包成.framework

[![](http://www.cocos2dev.com/wp-content/uploads/2012/07/11.png "1")](http://www.cocos2dev.com/wp-content/uploads/2012/07/11.png)

汗.....这个需要自己实现的是在太多了，我改天从我项目中把代码抽出来新建个demo工程。不好意思咯。有空闲时间的朋友可以自己找资料学学看。

&nbsp;