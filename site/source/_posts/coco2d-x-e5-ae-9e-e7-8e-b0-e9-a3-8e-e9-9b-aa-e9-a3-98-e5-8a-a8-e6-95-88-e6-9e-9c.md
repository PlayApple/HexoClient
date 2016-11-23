---
title: coco2d-x 实现风雪飘动效果
id: 102
categories:
  - Cocos2D-X
date: 2012-04-10 05:22:24
tags:
---

项目中的天气系统，需要用到风雪效果，这时模拟的风雪效果，在3d效果上，还有点不足，就是雪花的消失点应该按照雪花的缩放系数算出它该消失的位置。目前是用的在屏幕外面就从新移到原始位置。上个效果图：

[![](http://www.cocos2dev.com/wp-content/uploads/2012/04/13.png "1")](http://www.cocos2dev.com/wp-content/uploads/2012/04/13.png)

具体实现：

先定义几个雪花飘的方向

[cpp]

//先定义几个雪花的飘动方向

enum{

tag_batch_node=0,

tag_wind_none=1,

tag_wind_left=2,

tag_wind_right=3

};

//初始化雪花

voidGTWind::showWind(){

//初始的飘动方向

m_windDir=tag_wind_none;

//重力的值，负数向下

g=-4;

//每一帧构造的雪花书

maxV=2;

//当前屏幕最大雪花数

maxCount=150;

//风速

windVelocity=0;

winSize=CCDirector::sharedDirector()-&gt;getWinSize();

//使用SpriteBatchNode初始化，让所有雪花共享一块内存，

//而且只需要调用一次OPENGL绘画，就可以构造所有雪花，提高效率

CCSpriteBatchNode *spriteBatchNode = CCSpriteBatchNode::batchNodeWithFile(&quot;snow.png&quot;,300);

addChild(spriteBatchNode,0,tag_batch_node);

schedule(schedule_selector(GTWind::changeWind),5.0);

schedule(schedule_selector(GTWind::updataWind));

}

[/cpp]

上面使用的schedule来每一帧待用改变雪花位置，以及每5秒随机一次风向

现在随即风向，并调用函数动态改变当前的风速

[cpp]

//改变风的方向

voidGTWind::changeWind(){

int dir=arc4random()%3+1;

if(m_windDir==dir){

return;

}

m_windDir=dir;

switch (m_windDir) {

case tag_wind_none:

//规定雪花的最大速度

maxWindVelocity=0;

//动态改变速度

schedule(schedule_selector(GTWind::upDataWindVelocity));

break;

case tag_wind_left:

maxWindVelocity=20;

schedule(schedule_selector(GTWind::upDataWindVelocity));

break;

case tag_wind_right:

maxWindVelocity=-20;

schedule(schedule_selector(GTWind::upDataWindVelocity));

break;

default:

break;

}

}

[/cpp]

这个我以前就写过的，用于游戏中动态改变数值的

[cpp]

//动态改变当前的风速

voidGTWind::upDataWindVelocity(){

int addV=maxWindVelocity-windVelocity;

if(abs(addV)&gt;10){

windVelocity+=addV/10;

}elseif(abs(addV)&gt;2 &amp;&amp;abs(addV)&lt;=10){

windVelocity+=addV/abs(addV);

}else{

windVelocity=maxWindVelocity;

unschedule(schedule_selector(GTWind::upDataWindVelocity));

}

}

&amp;nbsp;

//刷新所有的雪花

voidGTWind::updataWind(ccTime dt){

CCSpriteBatchNode *temSpriteBatchNode = (CCSpriteBatchNode *)getChildByTag(tag_batch_node);

if(temSpriteBatchNode-&gt;getChildren()-&gt;count()&lt;maxCount){

for(int i=0;i&lt;maxV;i++){

//从SpriteBatchNode读取贴图创建sprite，并加入到SpriteBatchNode

CCSprite* sprite=CCSprite::spriteWithTexture(temSpriteBatchNode-&gt;getTexture());

temSpriteBatchNode-&gt;addChild(sprite);

sprite-&gt;setScale(1.0f - (arc4random()%5+5) / 10.0);

//初始化每个雪花的位置

if (windVelocity &gt;0)

sprite-&gt;setPosition(ccp(winSize.width+10,1.0*(arc4random()%((int)winSize.height+200))));

else

sprite-&gt;setPosition(ccp(-10,1.0*(arc4random()%((int)winSize.height+200))));

if (windVelocity &lt;3 &amp;&amp; windVelocity &gt; -3)

sprite-&gt;setPosition(ccp(1.0*(arc4random()%((int)winSize.height+240)),winSize.height+200));

}

}

//得到所有雪花，改变位置

CCArray* allSprite=temSpriteBatchNode-&gt;getChildren();

CCObject* pObject = NULL;

CCARRAY_FOREACH(allSprite, pObject){

CCSprite* pChild = (CCSprite*) pObject;

CCPoint pNow=pChild-&gt;getPosition();

pNow.x-=pChild-&gt;getScale()*windVelocity;

pNow.y+=g;

pChild-&gt;setPosition(pNow);

pChild-&gt;setRotation(pChild-&gt;getRotation()+0.1f);

if(pChild-&gt;getPosition().x&lt;-10 ||

pChild-&gt;getPosition().x&gt;winSize.width+10 ||

pChild-&gt;getPosition().y&lt;-10 ){

if (windVelocity &gt;0)

pChild-&gt;setPosition(ccp(winSize.width+10,1.0*(arc4random()%((int)winSize.height+200))));

else

pChild-&gt;setPosition(ccp(-10,1.0*(arc4random()%((int)winSize.height+200))));

if (windVelocity &lt;3 &amp;&amp; windVelocity &gt; -3)

pChild-&gt;setPosition(ccp(1.0*(arc4random()%((int)winSize.height+240)),winSize.height+10));

}

}

}

[/cpp]

好了，基本效果就这样了，可以改进的地方就是雪花的消失点，要根据每个雪花的缩放系数算出消失点，这样才有3D的效果