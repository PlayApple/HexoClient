---
title: cocos2d-x 创建帧动画
id: 36
categories:
  - IPhone
date: 2012-04-09 09:45:49
tags:
---

看到好多人问如何用cocos2d-x创建帧动画,其实用cocos2d-x很容易创建帧动画。我就写一遍吧。

`

void MyClass::initMyAnim()
{
/**
//可以在程序载入的时候预加载这些动画资源，然后在cache中读取
CCSpriteFrameCache* cache = CCSpriteFrameCache::sharedSpriteFrameCache();
cache-&gt;addSpriteFramesWithFile("run.plist", "run.png");
*/
CCSpriteFrameCache* cache = CCSpriteFrameCache::sharedSpriteFrameCache();
CCMutableArray&lt;CCSpriteFrame*&gt;* animFrames = new CCMutableArray&lt;CCSpriteFrame*&gt;(12);

char str[64] = {0};
for(int i = 1; i &lt;=12; i++)
{
sprintf(str, "run%04d.png", i);
CCSpriteFrame* frame = cache-&gt;spriteFrameByName( str );
animFrames-&gt;addObject(frame);
}

CCAnimation* animation = CCAnimation::animationWithFrames(animFrames,0.4f);
CCActionInterval* action=CCAnimate::actionWithAnimation(animation,true);
CCFiniteTimeAction *myRun= CCSequence::actions(action,CCCallFunc::actionWithTarget(this,callfunc_selector(MyClass::callBackRun)),NULL);
myRun-&gt;retain();
animFrames-&gt;release();
}

;
`
CCCallFunc用来做动画回调用，没有回调的话，就不用了。myRun-&gt;retain()后记得release掉。

OK，是不是很简单。记录一笔。