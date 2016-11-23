---
title: ' cocos2d-x 使用box2d实现物理弹跳效果'
tags:
  - Box2D
id: 78
categories:
  - Box2D
  - Cocos2D-X
date: 2012-04-10 05:09:53
---

一直对物理引擎灰常感兴趣。下面的是一个简单的物理效果演示。

弹跳的小球球，(呵呵，用了它自己的icon，没装PS，小球球没切好)

![](http://hi.csdn.net/attachment/201111/4/0_13204077638P52.gif)

使用cocos2d-x新建一个box2d的模板工程。

把HelloWorldScene.h换成下面的代码，（如果你用的是cocos2d，记得文件后最是.mm，oc和c++混编时文件要是mm）

[cpp]

//
// HelloWorldScene.h
// testBox2d
//
// Created by Yanghui Liu on 11-11-4.
// Copyright ard8 company 2011年. All rights reserved.
//
#ifndef __HELLO_WORLD_H__
#define __HELLO_WORLD_H__

#include &quot;cocos2d.h&quot;
#include &quot;Box2D.h&quot;
USING_NS_CC;

class HelloWorld : public cocos2d::CCLayer {
public:
 ~HelloWorld();
 HelloWorld();
 static cocos2d::CCScene* scene();
 void mInit();
 b2World *_world;
 b2Body *_body;
 CCSprite *_ball;
 void tick(cocos2d::ccTime dt);

};

#endif // __HELLO_WORLD_H__

[/cpp]

这个很简单，就不用说了。
下面是cpp文件

[cpp]

//
// HelloWorldScene.cpp
// testBox2d
//
// Created by Yanghui Liu on 11-11-4.
// Copyright ard8 company 2011年. All rights reserved.
//
#include &quot;HelloWorldScene.h&quot;
#include &quot;SimpleAudioEngine.h&quot;

using namespace cocos2d;
using namespace CocosDenshion;

#define PTM_RATIO 32

HelloWorld::HelloWorld()
{

}

HelloWorld::~HelloWorld()
{
 delete _world;
 _body = NULL;
 _world = NULL;
}

void HelloWorld::mInit()
{
 setIsAccelerometerEnabled(true);
 CCSize winSize = CCDirector::sharedDirector()-&gt;getWinSize();
 _ball = CCSprite::spriteWithFile(&quot;Icon.png&quot;);
 _ball-&gt;setPosition(ccp(100, 100));
 addChild(_ball);

 //创建世界的重力方向，为Y轴-30。即向下
 b2Vec2 gravity = b2Vec2(0.0f, -30.0f);

 //设置世界里面的对象是否可以休眠
 bool doSleep = true;
 _world = new b2World(gravity, doSleep);

 //创建世界
 b2BodyDef groundBodyDef;
 groundBodyDef.position.Set(0,0);
 b2Body *groundBody = _world-&gt;CreateBody(&amp;groundBodyDef);
 b2PolygonShape groundBox;
 b2FixtureDef boxShapeDef;
 boxShapeDef.shape = &amp;groundBox;
 groundBox.SetAsEdge(b2Vec2(0,0), b2Vec2(winSize.width/PTM_RATIO, 0));
 groundBody-&gt;CreateFixture(&amp;boxShapeDef);
 groundBox.SetAsEdge(b2Vec2(0,0), b2Vec2(0, winSize.height/PTM_RATIO));
 groundBody-&gt;CreateFixture(&amp;boxShapeDef);
 groundBox.SetAsEdge(b2Vec2(0, winSize.height/PTM_RATIO),
 b2Vec2(winSize.width/PTM_RATIO, winSize.height/PTM_RATIO));
 groundBody-&gt;CreateFixture(&amp;boxShapeDef);
 groundBox.SetAsEdge(b2Vec2(winSize.width/PTM_RATIO,
 winSize.height/PTM_RATIO), b2Vec2(winSize.width/PTM_RATIO, 0));
 groundBody-&gt;CreateFixture(&amp;boxShapeDef);

 //创建周围世界边框，在屏幕的周围一圈
 b2BodyDef ballBodyDef;
 ballBodyDef.type = b2_dynamicBody;
 ballBodyDef.position.Set(100/PTM_RATIO, 100/PTM_RATIO);
 ballBodyDef.userData = _ball;
 _body = _world-&gt;CreateBody(&amp;ballBodyDef);

 b2CircleShape circle;
 circle.m_radius = 26.0/PTM_RATIO;

 b2FixtureDef ballShapeDef;
 ballShapeDef.shape = &amp;circle;
 ballShapeDef.density = 1.0f;
 ballShapeDef.friction = 0.2f;
 ballShapeDef.restitution = 0.8f;
 _body-&gt;CreateFixture(&amp;ballShapeDef);

 schedule(schedule_selector(HelloWorld::tick));
}

void HelloWorld::tick(cocos2d::ccTime dt)
{
 _world-&gt;Step(dt, 10, 10);
 for(b2Body *b = _world-&gt;GetBodyList(); b; b=b-&gt;GetNext()) {
 if (b-&gt;GetUserData() != NULL) {
 CCSprite *ballData = (CCSprite *)b-&gt;GetUserData();
 ballData-&gt;setPosition(ccp(b-&gt;GetPosition().x * PTM_RATIO,
 b-&gt;GetPosition().y * PTM_RATIO));
 ballData-&gt;setRotation( -1 * CC_RADIANS_TO_DEGREES(b-&gt;GetAngle()));
 }
 }
}
CCScene* HelloWorld::scene()
{
 CCScene *scene = CCScene::node();
 HelloWorld* layer = new HelloWorld();
 layer-&gt;mInit();
 scene-&gt;addChild(layer);
 layer-&gt;release();
 return scene;
}[/cpp]

下面是相关资料的介绍，我就直接搬过来了。

## Box2D世界相关理论

在我们开始之前，让我们先交待一下Box2D具体是如何运作的。

你需要做的第一件事情就是，当使用cocos2d来为box2d创建一个world对象的时候。这个world对象管理物理仿真中的所有对象。

一旦我们已经创建了这个world对象，接下来需要往里面加入一些body对象。body对象可以随意移动，可以是怪物或者飞镖什么的，只要是参与碰撞的游戏对象都要为之创建一个相应的body对象。当然，也可以创建一些静态的body对象，用来表示游戏中的台阶或者墙壁等不可以移动的物体。

为了创建一个body对象，你需要做很多事情--首先，创建一个body定义结构，然后是body对象，再指定一个shap，再是fixture定义，然后再创建一个fixture对象。下面会一个一个解释刚刚这些东西。

*   你首先创建一个body定义结构体，用以指定body的初始属性，比如位置或者速度。
*   一旦创建好body结构体后，你就可以调用world对象来创建一个body对象了。
*   然后，你为body对象定义一个shape，用以指定你想要仿真的物体的几何形状。
*   接着创建一个fixture定义，同时设置之前创建好的shape为fixture的一个属性，并且设置其它的属性，比如质量或者摩擦力。
*   最后，你可以使用body对象来创建fixture对象，通过传入一个fixture的定义结构就可以了。
*   请注意，你可以往单个body对象里面添加很多个fixture对象。这个功能在你创建特别复杂的对象的时候非常有用。比如自行车，你可能要创建2个轮子，车身等等，这些fixture可以用关节连接起来。
只要你把所有需要创建的body对象都创建好之后，box2d接下来就会接管工作，并且高效地进行物理仿真---只要你周期性地调用world对象的step函数就可以了。

但是，请注意，box2d仅仅是更新它内部模型对象的位置--如果你想让cocos2d里面的sprite的位置也更新，并且和物理仿真中的位置相同的话，那么你也需要周期性地更新精灵的位置。

OK，继续我的代码：

创建了world对象的时候需要指定一个初始的重力向量，上面我设置y轴方向为-30，因此，所有的body都会往屏幕下面下落。

第二个参数设置世界里面的对象是否可以休眠，一个休眠的对象将不会花费处理时间，直到它与其实对象发生碰撞的时候才会“醒”过来。
接下来，创建一圈不可见的边：

首先创建一个body定义结构体，并且指定它应该放在左下角。
然后，使用world对象来创建body对象。（注意，这里一定要使用world对象来创建，不能直接new，因为world对象会做一些内存管理操作。）

接着，为屏幕的每一个边界创建一个多边形shape。这些“shape”仅仅是一些线段。注意，我们把像素转换成了“meter”。通过除以之前定义的比率来实现的。再创建一个fixture定义，指定shape为polygon shape。再使用body对象来为每一个shape创建一个fixture对象。注意：一个body对象可以包含许许多多的fixture对象。

接下来，我们创建篮球的body。这个步骤和之前创建地面的body差不多，但是有下面一些差别需要注意一下：
我们指定body的类型为dynamic body。默认值是static body，那意味着那个body不能被移动也不会参与仿真。很明显，我们想让篮球参与仿真。

设置body的user data属性为篮球精灵。你可以设置任何东西，但是，你设置成精灵会很方便，特别是当两个body碰撞的时候，你可以通过这个参数把精灵对象取出来，然后做一些逻辑处理。这里使用了一个不同的shape类型--circle shape。在这里，我们需要为这个fixture指定一些参数，因此，我们没有使用便捷方法来创建fixture。后面我们会讲到这些参数的具体意义。

第一件事情就是调用world对象的step方法，这样它就可以进行物理仿真了。这里的两个参数分别是“速度迭代次数”和“位置迭代次数”--你应该设置他们的范围在8-10之间。（译者：这里的数字越小，精度越小，但是效率更高。数字越大，仿真越精确，但同时耗时更多。8一般是个折中，如果学过数值分析，应该知道迭代步数的具体作用）。

接下来，我们要使我们的精灵匹配物理仿真。因此，我们遍历world对象里面的所有body，然后看body的user data属性是否为空，如果不为空，就可以强制转换成精灵对象。接下来，就可以根据body的位置来更新精灵的位置了。

&nbsp;

记得清理内存