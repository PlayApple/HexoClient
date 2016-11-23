---
title: cocos2d-x 2.x CCScale9Sprite 使用
tags:
  - CCScale9Sprite
  - 九宫格图
id: 295
categories:
  - Cocos2D-X
date: 2012-10-11 09:47:17
---

前段时间看CCEditBox的时候，发现里面有个利用9宫格图缩放图片的，也就是缩放带圆角的图片。

这个比较有用处，很多游戏中有很多不同尺寸的圆角图片作为背景。有了CCScale9Sprite之后，只需要提供一个非常小尺寸的圆角图片就可以自由缩放其他尺寸的圆角图。是个不错的东西。

&nbsp;

使用方法：

1、导入头文件及命名空间

#include "cocos-ext.h"
USING_NS_CC_EXT;

&nbsp;

2、初始化代码：

CCScale9Sprite* labBg1 = CCScale9Sprite::create("wd_bg_text.png");
labBg1-&gt;setAnchorPoint(ccp(.5,.5));
labBg1-&gt;setPreferredSize(CCSizeMake(255, 20));
labBg1-&gt;setPosition(ccp(size.width/2, size.height/2));
addChild(labBg1);

说明：

setPreferredSize 就是设置需要生成的尺寸大小。

看效果：

wd_bg_text.png  [![](http://www.cocos2dev.com/wp-content/uploads/2012/10/wd_bg_text.png "wd_bg_text")](http://www.cocos2dev.com/wp-content/uploads/2012/10/wd_bg_text.png)

运行后的效果：

[![](http://www.cocos2dev.com/wp-content/uploads/2012/10/demo.png "demo")](http://www.cocos2dev.com/wp-content/uploads/2012/10/demo.png)用来做游戏的圆角背景还是不错的。