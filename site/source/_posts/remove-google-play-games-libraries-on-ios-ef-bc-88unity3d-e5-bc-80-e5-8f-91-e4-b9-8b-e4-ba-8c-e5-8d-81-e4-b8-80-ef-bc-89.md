---
title: Remove Google Play Games libraries on iOS （Unity3D开发之二十一）
tags:
  - Google Play Games libraries
id: 643
categories:
  - Unity3D
date: 2015-09-09 03:50:19
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！ 
原文地址: http://blog.csdn.net/cocos2der/article/details/48313653

最近游戏刚接完Google Play Game，结果发现加入Google Play Game Unity Plugins 之后，导出Xcode工程之后，会默认加入Google Play的iOS版。

然而，我们并不希望iOS版加入Google Play的模块。咱们来看看如何移除Google Play Game iOS。

翻了下Google Play Game Plugins的代码，发现其实别人已经增加了宏定义选项来关闭iOS版导出。下面的NO_GPGS定义就是用来关闭iOS版的。

<pre class="lang:default decode:true " >#if (UNITY_ANDROID || (UNITY_IPHONE &amp;&amp; !NO_GPGS))</pre> 
定义NO_GPGS，用来关闭其iOS模块：
[![NO_GPGS](http://www.cocos2dev.com/wp-content/uploads/2015/09/NO_GPGS.png)](http://www.cocos2dev.com/wp-content/uploads/2015/09/NO_GPGS.png)
1\. Build Setttings-iOS-Player Settings-Other Setttings，在Scripting Define Symbol中添加NO_GPGS
2\. 移除Plugins/iOS下相关iOS文件（版本不同可能文件不一样）： 
    CustomWebViewApplication.h
    CustomWebViewApplication.mm
    GPGSAppController.h
    GPGSAppController.mm
Ok，这样就完成了，是不是很简单。