---
title: cocos2d-x 2.0 动画的创建（利用swf创建动画）
tags:
  - 动画
id: 279
categories:
  - Cocos2D-X
date: 2012-07-23 13:20:31
---

我之前介绍了cocos2d-x各种动画的创建方式，但是2.0修改了一些方法，又有很多朋友问我怎么创建动画，今天我再介绍下2.0的动画创建方式，希望大家看到了，能够自己学着创建其他方式的动画。

&nbsp;

一、使用swf创建动画。

1、首先使用tp工具将swf导出成plist文件。

2、创建动画的方法：

[cpp]

CCActionInterval* SAAnimationManager::createRFAnimFormSwf(const char* swfName,int frames){

char str1[100] = {0};

char str2[100] = {0};

CCSpriteFrameCache *cache = CCSpriteFrameCache::sharedSpriteFrameCache();

sprintf(str1, &quot;%s.plist&quot;, swfName);

sprintf(str2, &quot;%s.png&quot;, swfName);

cache-&gt;addSpriteFramesWithFile(str1,str2);

CCArray* animFrames = CCArray::create(15);

for(int i = 0; i &lt; frames; i++){

sprintf(str2, &quot;%s.swf/%04d&quot;, swfName,i);

CCSpriteFrame *frame = cache-&gt;spriteFrameByName(str2);

animFrames-&gt;addObject(frame);

}

CCAnimation *animation = CCAnimation::create(animFrames, kDelayPerUnit);

cache-&gt;removeSpriteFramesFromFile(str1);

return CCRepeatForever::create(CCAnimate::create(animation));

}

[/cpp]

&nbsp;

参数介绍：

const char* swfName ：swf的名字，记住swf的名字最好和plist的名字一样。如果你的swf名字和导出的plist名字不一样，你会发现pilst里面的每一帧的名字是swf的名字，plist是你取的名字，就会有两个不同的名字，这样在加载里面每一帧的时候就注意了名字了。

int frames：swf的帧数。

&nbsp;

这样就会返回一个永久性的动画，你就可以用一个sprite-&gt;runAction()的方式显示这个动作了。

&nbsp;