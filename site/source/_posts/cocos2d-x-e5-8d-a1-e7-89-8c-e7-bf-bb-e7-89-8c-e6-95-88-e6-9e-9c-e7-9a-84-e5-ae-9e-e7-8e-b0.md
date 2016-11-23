---
title: cocos2d-x 卡牌翻牌效果的实现
tags:
  - CCOrbitCamera
  - cocos2d-x
  - 翻牌效果
id: 428
categories:
  - Cocos2D-X
date: 2013-06-17 13:58:52
---

[![card](http://www.cocos2dev.com/wp-content/uploads/2013/06/card.png)](http://www.cocos2dev.com/wp-content/uploads/2013/06/card.png)

刚有人问我cocos2dx能否实现卡牌的翻牌效果，其实cocos2dx有个action就是这个效果，晚上随便写了个效果。需要的可以看看。

上面的效果图是ipad模拟器，所以帧率只有30，实际是60.

这个能实现翻牌的action就是CCOrbitCamera。

[cpp]

static CCOrbitCamera* create(float t, float radius, float deltaRadius, float angleZ, float deltaAngleZ, float angleX, float deltaAngleX);

[/cpp]

参数分别为旋转的时间，起始半径，半径差，起始z角，旋转z角差，起始x角，旋转x角差

下面是我封装的cardSprite，

请自己加入自己的工程，

用法：

[cpp]

//创建翻牌sprite, 参数：卡片里面的图，卡片的封面，翻牌所花时间

CardSprite* card = CardSprite::create(&quot;1.png&quot;, &quot;2.png&quot;, 2);

card-&gt;setPosition(ccp(size.width * .5, size.height * .5));

addChild(card);

card-&gt;openCard();//开始翻牌

[/cpp]

CardSprite.h

[cpp]

//

//  CardSprite.h

//  Cocos2dev.com

//

//  Created by LiuYanghui on 13-6-17.

//

//

&amp;nbsp;

#ifndef __Test__CardSprite__

#define __Test__CardSprite__

&amp;nbsp;

#include &quot;cocos2d.h&quot;

USING_NS_CC;

&amp;nbsp;

class CardSprite : public CCSprite

{

public:

CardSprite();

~CardSprite();

static CardSprite* create(const char* inCardImageName, const char* outCardImageName, float duration);

virtual bool init(const char* inCardImageName, const char* outCardImageName, float duration);

private:

bool m_isOpened;

CCActionInterval* m_openAnimIn;

CCActionInterval* m_openAnimOut;

void initData(const char* inCardImageName, const char* outCardImageName, float duration);

public:

void openCard();

};

&amp;nbsp;

#endif /* defined(__Test__CardSprite__) */

[/cpp]

&nbsp;

CardSprite.cpp

[cpp]

//

//  CardSprite.cpp

//  Cocos2dev.com

//

//  Created by LiuYanghui on 13-6-17.

//

//

&amp;nbsp;

#include &quot;CardSprite.h&quot;

&amp;nbsp;

#define kInAngleZ        270 //里面卡牌的起始Z轴角度

#define kInDeltaZ        90  //里面卡牌旋转的Z轴角度差

&amp;nbsp;

#define kOutAngleZ       0   //封面卡牌的起始Z轴角度

#define kOutDeltaZ       90  //封面卡牌旋转的Z轴角度差

&amp;nbsp;

enum {

tag_inCard = 1,

tag_outCard

};

&amp;nbsp;

CardSprite::CardSprite()

{

}

&amp;nbsp;

CardSprite::~CardSprite()

{

m_openAnimIn-&gt;release();

m_openAnimOut-&gt;release();

}

&amp;nbsp;

CardSprite* CardSprite::create(const char* inCardImageName, const char* outCardImageName, float duration)

{

CardSprite *pSprite = new CardSprite();

if (pSprite &amp;&amp; pSprite-&gt;init(inCardImageName, outCardImageName, duration))

{

pSprite-&gt;autorelease();

return pSprite;

}

CC_SAFE_DELETE(pSprite);

return NULL;

}

&amp;nbsp;

bool CardSprite::init(const char* inCardImageName, const char* outCardImageName, float duration)

{

if (!CCSprite::init())

{

return false;

}

initData(inCardImageName, outCardImageName, duration);

return true;

}

&amp;nbsp;

#pragma mark - initData

void CardSprite::initData(const char* inCardImageName, const char* outCardImageName, float duration)

{

m_isOpened = false;

CCSprite* inCard = CCSprite::create(inCardImageName);

inCard-&gt;setPosition(CCPointZero);

inCard-&gt;setVisible(false);

inCard-&gt;setTag(tag_inCard);

addChild(inCard);

CCSprite* outCard = CCSprite::create(outCardImageName);

outCard-&gt;setPosition(CCPointZero);

outCard-&gt;setTag(tag_outCard);

addChild(outCard);

m_openAnimIn = (CCActionInterval*)CCSequence::create(CCDelayTime::create(duration * .5),

CCShow::create(),

CCOrbitCamera::create(duration * .5, 1, 0, kInAngleZ, kInDeltaZ, 0, 0),

NULL);

m_openAnimIn-&gt;retain();

m_openAnimOut = (CCActionInterval *)CCSequence::create(CCOrbitCamera::create(duration * .5, 1, 0, kOutAngleZ, kOutDeltaZ, 0, 0),

CCHide::create(),

CCDelayTime::create(duration * .5),

NULL);

m_openAnimOut-&gt;retain();

}

&amp;nbsp;

#pragma mark - public func

void CardSprite::openCard()

{

CCSprite* inCard = (CCSprite*)getChildByTag(tag_inCard);

CCSprite* outCard = (CCSprite*)getChildByTag(tag_outCard);

inCard-&gt;runAction(m_openAnimIn);

outCard-&gt;runAction(m_openAnimOut);

}

[/cpp]