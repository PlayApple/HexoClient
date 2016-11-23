---
title: 'cocos2d-x中保存用户游戏数据CCUserDefault '
tags:
  - CCUserDefault
id: 54
categories:
  - Cocos2D-X
date: 2012-04-09 13:11:44
---

正在做项目中有很多游戏数据要保存，常见的玩家数据这些比较简单的可以用CCUserDefault。它是cocos2d-x用来存取基本数据类型用的。保存为XML文件格式。

主要方法：(和java的map很像，键值对，应该很容易懂的)

[cpp]

void    setBoolForKey(const char* pKey, bool value);
void    setIntegerForKey(const char* pKey, int value);
void    setFloatForKey(const char* pKey, float value);
void    setDoubleForKey(const char* pKey, double value);
void    setStringForKey(const char* pKey, const std::string &amp; value);

[/cpp]

通过键读取数据，如果键不存在，可以设置一个defaultValue返回自己想要的值。

[cpp]

bool    getBoolForKey(const char* pKey, bool defaultValue = false);
int    getIntegerForKey(const char* pKey, int defaultValue = 0);
float    getFloatForKey(const char* pKey, float defaultValue=0.0f);
double    getDoubleForKey(const char* pKey, double defaultValue=0.0);
std::string    getStringForKey(const char* pKey, const std::string &amp; defaultValue = &quot;&quot;);

[/cpp]

首次运行程序时可以去生成xml文件CCUserDefault::sharedUserDefault()-&gt;setIntegerForKey("MyGold", 0);

这样就可以生成一个xml文件。不过这种硬代码我不是很喜欢。

每次调用的时候要写很长的代码。可以建议搞几个宏，毕竟CCUserDefault的get，set实在太长了。

[cpp]

#define SaveStringToXML CCUserDefault::sharedUserDefault()-&gt;setStringForKey

#define SaveIntegerToXML CCUserDefault::sharedUserDefault()-&gt;setIntegerForKey

#define SaveBooleanToXML CCUserDefault::sharedUserDefault()-&gt;setBoolForKey

#define LoadStringFromXML CCUserDefault::sharedUserDefault()-&gt;getStringForKey

#define LoadIntegerFromXML CCUserDefault::sharedUserDefault()-&gt;getIntegerForKey

#define LoadBooleanFromXML CCUserDefault::sharedUserDefault()-&gt;getBoolForKey

[/cpp]

如何首次生成判断文件是否存在呢

其实可以利用get方法去获取。

[cpp]

       if ( !LoadBooleanFromXML(&quot;_IS_EXISTED&quot;))

       {

              initUserData();            

              SaveBooleanToXML(&quot;_IS_EXISTED&quot;, true);

       }

[/cpp]

对了，ccUserDefault在0.9.1版本会在安卓平台下crash掉，更新源代码就OK了