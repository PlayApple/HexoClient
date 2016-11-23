---
title: cocos2d-x touch事件
tags:
  - 手势
id: 88
categories:
  - Cocos2D-X
date: 2012-04-10 05:16:17
---

头文件申明：

virtualbool ccTouchBegan(CCTouch *pTouch,CCEvent *pEvent);

virtualvoid ccTouchEnded(CCTouch *pTouch,CCEvent *pEvent);

//用于点击的优先级设置

virtualvoid registerWithTouchDispatcher(void);

//实现，基本都是一样的，就只实现一个了。

voidStory::ccTouchesEnded(CCSet* touches,CCEvent* event)

{

CCSetIteratorit;

CCTouch* touch;

for( it = touches-&gt;begin(); it != touches-&gt;end(); it++)

{

touch = (CCTouch*)(*it);

if(!touch)

break;

CCPoint location = touch-&gt;locationInView(touch-&gt;view());

location =CCDirector::sharedDirector()-&gt;convertToGL(location);

//location.x和location.y就是点击的坐标点

}

//如果点击获取不到，可以设置下优先级

voidStory::registerWithTouchDispatcher(void)

{

//CCTouchDispatcher::sharedDispatcher()-&gt;addTargetedDelegate(this, kCCMenuTouchPriority+1, false);

CCTouchDispatcher::sharedDispatcher()-&gt;addTargetedDelegate(this,1, false);

}