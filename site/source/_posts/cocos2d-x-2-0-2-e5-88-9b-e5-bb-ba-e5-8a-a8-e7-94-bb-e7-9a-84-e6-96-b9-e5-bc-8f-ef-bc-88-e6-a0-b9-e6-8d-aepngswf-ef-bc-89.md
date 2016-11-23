---
title: cocos2d-x 2.0.2 创建动画的方式（根据png/swf）
id: 292
categories:
  - Cocos2D-X
date: 2012-09-25 11:23:05
tags:
---

很早以前我写过了创建动画的一些方式，那都是1.x版本的时候，现在总有人问我，说创建不了动画，代码有问题。

&nbsp;

2.x之后改了一些函数，其实出错了，自己看下应该会改的。

&nbsp;

我今天就把创建动画的几种方式写一下。我封装了这个类，大家导入自己工程，静态方法调用就可以了。

头文件

BYAnimationTool.h

[cpp]

//

//  BYAnimationTool.h

//  COG

//

//  Created by Yanghui Liu on 12-7-26.

//  Copyright (c) 2012年 BoyoJoy. All rights reserved.

//

&amp;nbsp;

#ifndef COG_BYAnimationTool_h

#define COG_BYAnimationTool_h

&amp;nbsp;

&amp;nbsp;

#include &quot;cocos2d.h&quot;

USING_NS_CC;

&amp;nbsp;

class BYAnimationTool {

public:

static CCActionInterval* createAnimFormSwf(const char* swfName);

static CCActionInterval* createAnimFormSwf(const char* swfName,float frameTime);

static CCActionInterval* createRFAnimFormSwf(const char* swfName);

static CCActionInterval* createRFAnimFormSwf(const char* swfName,float frameTime);

static CCActionInterval* createRFAnimFormPng(const char* pngName,const char* frameName,int beginFrameIndex);

};

&amp;nbsp;

#endif

[/cpp]

BYAnimationTool.cpp文件

[cpp]

//

//  BYAnimationTool.cpp

//  COG

//

//  Created by Yanghui Liu on 12-7-26.

//  Copyright (c) 2012年 BoyoJoy. All rights reserved.

//

&amp;nbsp;

#include &quot;BYAnimationTool.h&quot;

&amp;nbsp;

#define kDelayPerUnit 0.08f

&amp;nbsp;

//将SWF转成一次性动画

CCActionInterval* BYAnimationTool::createAnimFormSwf(const char* swfName){

return createAnimFormSwf(swfName,kDelayPerUnit);

}

&amp;nbsp;

//将SWF转成一次性动画

CCActionInterval* BYAnimationTool::createAnimFormSwf(const char* swfName,float frameTime){

char str1[100] = {0};

char str2[100] = {0};

CCSpriteFrameCache *cache = CCSpriteFrameCache::sharedSpriteFrameCache();

sprintf(str1, &quot;%s.plist&quot;, swfName);

sprintf(str2, &quot;%s.pvr.ccz&quot;, swfName);

cache-&gt;addSpriteFramesWithFile(str1,str2);

CCArray* animFrames = CCArray::create();

int i = 0;

do {

sprintf(str2, &quot;%s.swf/%04d&quot;, swfName,i);

CCSpriteFrame *frame = cache-&gt;spriteFrameByName(str2);

if (frame) {

animFrames-&gt;addObject(frame);

}else {

break;

}

} while (++i);

CCAnimation *animation = CCAnimation::createWithSpriteFrames(animFrames, frameTime);

cache-&gt;removeSpriteFramesFromFile(str1);

return CCAnimate::create(animation);

}

&amp;nbsp;

//将SWF转成永久性动画

CCActionInterval* BYAnimationTool::createRFAnimFormSwf(const char* swfName){

return createRFAnimFormSwf(swfName,kDelayPerUnit);

}

&amp;nbsp;

//将SWF转成永久性动画 可以自定义帧率

CCActionInterval* BYAnimationTool::createRFAnimFormSwf(const char* swfName,float frameTime){

return CCRepeatForever::create(createAnimFormSwf(swfName,frameTime));

}

&amp;nbsp;

//将PNG序列图转成永久性动画

CCActionInterval* BYAnimationTool::createRFAnimFormPng(const char* pngName,const char* frameName,int beginFrameIndexe){

char str1[100] = {0};

char str2[100] = {0};

CCSpriteFrameCache *cache = CCSpriteFrameCache::sharedSpriteFrameCache();

sprintf(str1, &quot;%s.plist&quot;, pngName);

sprintf(str2, &quot;%s.pvr.ccz&quot;, pngName);

cache-&gt;addSpriteFramesWithFile(str1,str2);

CCArray* animFrames = CCArray::create();

int i = beginFrameIndexe;

do {

sprintf(str2, &quot;%s%04d.png&quot;, frameName,i);

CCSpriteFrame *frame = cache-&gt;spriteFrameByName(str2);

if (frame) {

animFrames-&gt;addObject(frame);

}else {

break;

}

} while (++i);

CCAnimation *animation = CCAnimation::createWithSpriteFrames(animFrames, kDelayPerUnit);

cache-&gt;removeSpriteFramesFromFile(str1);

return CCRepeatForever::create(CCAnimate::create(animation));

}

[/cpp]

简单写了下注释，应该看得懂的。

&nbsp;

使用方法：

[cpp]

CCSize winSize = CCDirector::sharedDirector()-&gt;getWinSize();

CCSprite* sprite = CCSprite::create();

sprite-&gt;setPosition(ccp(winSize.width*.5,winSize.height*.5));

addChild(sprite);

sprite-&gt;runAction(BYAnimationTool::createRFAnimFormSwf(&quot;test&quot;));

[/cpp]

&nbsp;

BYAnimationTool::createRFAnimFormSwf("test") 是创建一个永久性重复播放的动画。

<span style="color: #ff0000;">注意：</span>

1、所有方法传入的文件名不要后缀。

2、png和swf是我使用了TexturePaker压缩了的。