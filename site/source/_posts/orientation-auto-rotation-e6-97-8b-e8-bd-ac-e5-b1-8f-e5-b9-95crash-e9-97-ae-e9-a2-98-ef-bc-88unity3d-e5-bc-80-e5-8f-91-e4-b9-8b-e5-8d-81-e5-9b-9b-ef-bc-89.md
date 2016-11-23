---
title: Orientation Auto Rotation旋转屏幕crash问题（Unity3D开发之十四）
tags:
  - AutoRotation
  - Orientation
  - Unity5
id: 612
categories:
  - Unity3D
date: 2015-03-08 07:11:24
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！

原文地址: http://blog.csdn.net/cocos2der/article/details/44133127

今天运行一个场景时候，welcome场景可以旋转，进入主场景后发现只要旋转手机屏幕就会crash。

<pre class="lang:default decode:true " >*** Terminating app due to uncaught exception 'NSInternalInconsistencyException',
reason: 'UnityDefaultViewController should be used 
only if unity is set to autorotate'</pre> 

出错信息已经显示是使用auto rotation导致的。

Unity Build setting里面已经是Auto Rotation，为何主场景无法旋转？

而且错误表示：是我在调用autorotate，但是我设置的模式却不是autorotate。

最后全局搜索了下，果然有测试代码手工设置了屏幕方向为Left。修改为自动后，问题解决。

<pre class="lang:default decode:true " >Screen.orientation = ScreenOrientation.AutoRotation;</pre> 

问题虽然是个小问题，但是想起了那谁谁前天线上游戏由于开发写的测试代码跑到线上，导致玩家多了一个装了很多道具的宝箱。

所以，谁要是说：”这个功能先这样就好了， 等正式配置出来了，我在修改。“，我保证不掐死你…