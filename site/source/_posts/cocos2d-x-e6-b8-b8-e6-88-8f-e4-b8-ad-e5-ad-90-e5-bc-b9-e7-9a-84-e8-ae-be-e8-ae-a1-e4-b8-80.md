---
title: Cocos2d-x 游戏中子弹的设计(一)
tags:
  - cocos2d-x
  - 子弹
id: 379
categories:
  - Cocos2D-X
date: 2013-02-04 09:31:05
---

FPS游戏中用的最多的就是子弹了，这几天有不少人问我游戏中如何设计子弹？大量子弹显示的时候，游戏会不会卡？

&nbsp;

今天有点时间，我针对这两个问题，简单的写了一个子弹的发射。

&nbsp;

效果图：

[![](http://www.cocos2dev.com/wp-content/uploads/2013/02/bullet.jpg "bullet")](http://www.cocos2dev.com/wp-content/uploads/2013/02/bullet.jpg)

&nbsp;

首先我们来理下如何设计的思路。

&nbsp;

一、首先一个简单的关系：子弹肯定是由武器发射的，武器是由人物操作的。

&nbsp;

二、也就是说子弹Bullet类只负责创建一个新子弹、子弹的移动和子弹的消失、碰撞。

Bullet类提供给外界接口：增加一个新子弹（以及子弹的速度）。

&nbsp;

三、武器weapon类给负责调用Bullet类创建一个新子弹。同时，weapon类也要负责计算发射子弹的速度，以及增加新子弹的时间间隔。

weapon类提供外界借口：开枪（发射子弹）。

&nbsp;

四、人物类调用weap类的开枪接口。

&nbsp;

OK，简单的整理了下（其实也挺简单的，是吧）。

&nbsp;

下面开始重要的实现，子弹的实现：

&nbsp;

子弹bullet类的职能很单一，只负责创建一个新子弹、子弹的移动和子弹的消失、碰撞。

直接上代码吧：

Bullet.h

[cpp]

//

//  Bullet.h

//  FireDemo

//

//  Created by LiuYanghui on 13-2-4.

//

//

&amp;nbsp;

#ifndef __FireDemo__Bullet__

#define __FireDemo__Bullet__

&amp;nbsp;

#include &quot;cocos2d.h&quot;

USING_NS_CC;

&amp;nbsp;

class Bullet : public CCLayer {

public:

Bullet();

~Bullet();

CREATE_FUNC(Bullet);

virtual bool init();

void addNewBullet();//增加一个新子弹

private:

CCSize winSize;

CCArray* m_allBulletArray;//保存所有已存在的子弹对象

CCSpriteBatchNode* m_bulletSprite;//使用BatchNode构建所有的子弹，减少gl绘画次数

void initData();

void moveBullet(float dt);//移动子弹

};

&amp;nbsp;

&amp;nbsp;

#endif /* defined(__FireDemo__Bullet__) */

[/cpp]

&nbsp;

Bullet.cpp

[cpp]

//

//  Bullet.cpp

//  FireDemo

//

//  Created by LiuYanghui on 13-2-4.

//

//

&amp;nbsp;

#include &quot;Bullet.h&quot;

&amp;nbsp;

#define MOVE_SPEED      100  //子弹的速度

#define REMOVE_POSY     -768 //子弹消失的位置，根据自己项目定

&amp;nbsp;

Bullet::Bullet(){

}

&amp;nbsp;

Bullet::~Bullet(){

m_allBulletArray-&gt;release();

}

&amp;nbsp;

bool Bullet::init(){

if (!CCLayer::init()) {

return false;

}

initData();

return true;

}

&amp;nbsp;

void Bullet::initData(){

winSize = CCDirector::sharedDirector()-&gt;getWinSize();

//创建对象数组，保存已存在的子弹

m_allBulletArray = CCArray::create();

m_allBulletArray-&gt;retain();

//子弹贴图

m_bulletSprite = CCSpriteBatchNode::create(&quot;bullet.png&quot;);

m_bulletSprite-&gt;setPosition(CCPointZero);

addChild(m_bulletSprite);

//更新子弹速度

schedule(schedule_selector(Bullet::moveBullet));

}

&amp;nbsp;

//增加一个新子弹

void Bullet::addNewBullet(){

//构建一个子弹，添加到BatchNode中。

CCSprite* newBullet = CCSprite::createWithTexture(m_bulletSprite-&gt;getTexture());

newBullet-&gt;setPosition(CCPointZero);

m_bulletSprite-&gt;addChild(newBullet);

//添加子弹到已存在子弹数组

m_allBulletArray-&gt;addObject(newBullet);

}

&amp;nbsp;

//移动子弹

void Bullet::moveBullet(float dt){

//遍历更新每个子弹的位置

for (int i = 0; i &lt; m_allBulletArray-&gt;count(); ++i) {

//获取一个子弹对象

CCSprite* bullet = (CCSprite*)m_allBulletArray-&gt;objectAtIndex(i);

//更新位置

bullet-&gt;setPositionY(bullet-&gt;getPositionY()-MOVE_SPEED*dt);

//如果已到达消失位置，就移除该子弹

if (bullet-&gt;getPositionY() &lt;= REMOVE_POSY) {

m_allBulletArray-&gt;removeObjectAtIndex(i);

m_bulletSprite-&gt;removeChild(bullet, true);

}

}

}

[/cpp]

&nbsp;

子弹生成了，现在写个简单的调用场景吧。

GameScene.h

[cpp]

//

//  GameScene.h

//  FireDemo

//

//  Created by LiuYanghui on 13-2-4.

//

//

&amp;nbsp;

#ifndef __FireDemo__GameScene__

#define __FireDemo__GameScene__

&amp;nbsp;

#include &quot;cocos2d.h&quot;

USING_NS_CC;

&amp;nbsp;

class Bullet;

class GameScene : public CCLayer {

public:

GameScene();

~GameScene();

CREATE_FUNC(GameScene);

static CCScene* scene();

virtual bool init();

virtual void registerWithTouchDispatcher(void);

virtual bool ccTouchBegan(CCTouch *pTouch, CCEvent *pEvent);

virtual void ccTouchMoved(CCTouch *pTouch, CCEvent *pEvent);

virtual void ccTouchEnded(CCTouch *pTouch, CCEvent *pEvent);

virtual void ccTouchCancelled(CCTouch *pTouch, CCEvent *pEvent);

private:

CCSize winSize;

Bullet* m_bullet;

void initData();

};

&amp;nbsp;

#endif /* defined(__FireDemo__GameScene__) */

&amp;nbsp;

[/cpp]

&nbsp;

&nbsp;

GameScene.cpp

&nbsp;

[cpp]

//

//  GameScene.cpp

//  FireDemo

//

//  Created by LiuYanghui on 13-2-4.

//

//

&amp;nbsp;

#include &quot;GameScene.h&quot;

#include &quot;Bullet.h&quot;

&amp;nbsp;

GameScene::GameScene(){

}

&amp;nbsp;

GameScene::~GameScene(){

}

&amp;nbsp;

CCScene* GameScene::scene(){

CCScene* scene = CCScene::create();

GameScene* layer = GameScene::create();

scene-&gt;addChild(layer);

return scene;

}

&amp;nbsp;

bool GameScene::init(){

if (!CCLayer::init()) {

return false;

}

initData();

return true;

}

&amp;nbsp;

void GameScene::initData(){

winSize = CCDirector::sharedDirector()-&gt;getWinSize();

CCSprite* mainBg = CCSprite::create(&quot;bg.jpg&quot;);

mainBg-&gt;setPosition(ccp(winSize.width*.5,winSize.height*.5));

addChild(mainBg);

CCSprite* tank = CCSprite::create(&quot;tank.png&quot;);

tank-&gt;setPosition(ccp(winSize.width*.5,winSize.height*.8));

addChild(tank);

//创建子弹对象

m_bullet = Bullet::create();

m_bullet-&gt;setPosition(winSize.width*.5,winSize.height*.8);

addChild(m_bullet);

setTouchEnabled(true);

}

&amp;nbsp;

&amp;nbsp;

&amp;nbsp;

void GameScene::registerWithTouchDispatcher(void){

CCDirector::sharedDirector()-&gt;getTouchDispatcher()-&gt;addTargetedDelegate(this, 0, true);

}

&amp;nbsp;

bool GameScene::ccTouchBegan(CCTouch *pTouch, CCEvent *pEvent)

{

//每次点击添加一个新子弹

m_bullet-&gt;addNewBullet();

return true;

}

&amp;nbsp;

void GameScene::ccTouchMoved(CCTouch *pTouch, CCEvent *pEvent)

{

&amp;nbsp;

}

&amp;nbsp;

void GameScene::ccTouchEnded(CCTouch *pTouch, CCEvent *pEvent)

{

&amp;nbsp;

}

&amp;nbsp;

void GameScene::ccTouchCancelled(CCTouch *pTouch, CCEvent *pEvent)

{

&amp;nbsp;

}

&amp;nbsp;

[/cpp]

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;