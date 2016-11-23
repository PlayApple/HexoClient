---
title: cocos2d-x 屏蔽场景的Touch事件
id: 157
categories:
  - Cocos2D-X
date: 2012-04-29 11:56:33
tags:
---

cocos2d-x有个问题，即使暂停CCScene运行，触摸仍然有效，有些菜单和按钮仍然会被触发。

所以有的时候我们要手动屏蔽触摸，尤其是在弹出计费画面的，或者使用一些本平台自带控件的时候。

做法有几种：

第一种方案是：每一个继承自CCLayer的类都可以关闭触摸，使用这个函数

[cpp]setIsTouchEnabled(false);[/cpp]

在暂停sence时，我们只需要关闭那些主要的CCLayer即可，也就是当前CCScene的主CCLayer和相关的CCMenu等。

但这种方法，有时候会造成莫名其妙的crash。调试发现，如果在同一个frame的同一个函数中，先关闭再恢复触摸，就可能造成crash。crash的原因是引擎在派发触摸事件时，发现响应对象的列表为空，直接触发断言。

第二种方案是：写一个CCLayer，把所有优先级调至最高，并直接覆盖到当前CCSence主CCLayer上。

经过试验，这种方式非常简单有效，重用性也很高。

[cpp]

class NoTouchLayer : public cocos2d::CCLayer{
public:
 // Here's a difference. Method 'init' in cocos2d-x returns bool, instead of returning 'id' in cocos2d-iphone
 virtual bool init();

// implement the &quot;static node()&quot; method manually
LAYER_NODE_FUNC(NoTouchLayer);

virtualvoid registerWithTouchDispatcher();

virtualbool ccTouchBegan (cocos2d::CCTouch *pTouch, cocos2d::CCEvent *pEvent);
virtualvoid ccTouchMoved (cocos2d::CCTouch *pTouch, cocos2d::CCEvent *pEvent);
virtualvoid ccTouchEnded (cocos2d::CCTouch *pTouch, cocos2d::CCEvent *pEvent);

};

[/cpp]

实现方法

[cpp]

bool NoTouchLayer::init(){
if (!CCLayer::init() )
{
return false;
}

setIsTouchEnabled(true);

return true;
}

void NoTouchLayer::registerWithTouchDispatcher(){
CCTouchDispatcher::sharedDispatcher()-&gt;addTargetedDelegate(this, numeric_limits &lt;int&gt; ::min() , true);

//使用int最小值做最高优先级,并且吞掉事件true

CCLayer::registerWithTouchDispatcher();
}

bool NoTouchLayer::ccTouchBegan (CCTouch *pTouch, CCEvent *pEvent){
return true;
}

void NoTouchLayer::ccTouchMoved (CCTouch *pTouch, CCEvent *pEvent){  }

void NoTouchLayer::ccTouchEnded (CCTouch *pTouch, CCEvent *pEvent){  }

[/cpp]

这个类的使用也很简单，直接addChild，removeChild即可。注意清理，注意addChild的时候要给一个足够大的z轴深度值。