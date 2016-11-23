---
title: 'cocos2d-x 中使用加减定位后的注意问题 '
id: 44
categories:
  - Cocos2D-X
date: 2012-04-09 12:38:36
tags:
---

昨天项目中，在setPosition的时候，开始是根据屏幕大小来定位的，有时会出现一点点偏差，就直接加减了坐标点。

labName-&gt;setPosition(ccp(screenSize.width*0.5-100,screenSize.height*0.5));

看到了吧，我偷懒，直接减掉了100，在低清晰版上没有问题，我换成高清晰版

enableRetinaDisPlay(true); 

这时，BUG了，定位出现了偏差。![疑问](http://static.blog.csdn.net/xheditor/xheditor_emot/default/doubt.gif)为什么呢。。。

研究后发现，原来这个函数就是将原来的坐标统一放大了一倍，这样我们应该是减50。所以建议还是不要使用加减坐标点。最好使用百分比，虽然麻烦点。图片的大小可以使用getContentSize()函数获取。

&nbsp;

当然CCLabelTTF中的字体也是同样的会出现这个问题。用的时候要注意点。