---
title: Cocos2d-x 2.0.4 在iPad iOS 4.3中JPG图片Alpha显示错误的解决办法
tags:
  - cocos2d-x
id: 376
categories:
  - Cocos2D-X
date: 2013-01-29 07:16:55
---

今天安装一个iOS 4.3的iPad的时候，突然发现游戏中所有的JPG图变得非常黑，实际上就是JPG变成了半透明的。之前都是在5.1以上系统上运行，所以没发现该问题。

&nbsp;

看了下代码，发现是CCImage对JPG格式图片设置Alpha的时候错误了。

&nbsp;

解决办法：

&nbsp;

找到/libs/cocos2dx/platform/ios/CCImage.mm 文件

&nbsp;

打开后，找到这段代码：

[cpp]

pImageinfo-&gt;hasAlpha = (info == kCGImageAlphaPremultipliedLast)
 || (info == kCGImageAlphaPremultipliedFirst)
 || (info == kCGImageAlphaLast)
 || (info == kCGImageAlphaFirst);

[/cpp]

替换成下面的：

[cpp]
pImageinfo-&gt;hasAlpha = ((info == kCGImageAlphaNoneSkipLast) ||
 (info == kCGImageAlphaPremultipliedLast) ||
 (info == kCGImageAlphaPremultipliedFirst) ||
 (info == kCGImageAlphaLast) ||
 (info == kCGImageAlphaFirst) ? YES : NO);

[/cpp]

在运行下，看看是不是好了。

一般使用JPG主要是为了减少容量，所以大尺寸的背景图都会用JPG格式。
但是有一点要知道加载JPG的速度比加载PNG的速度要慢，所以什么地方用JPG，要权衡权衡。

&nbsp;

&nbsp;