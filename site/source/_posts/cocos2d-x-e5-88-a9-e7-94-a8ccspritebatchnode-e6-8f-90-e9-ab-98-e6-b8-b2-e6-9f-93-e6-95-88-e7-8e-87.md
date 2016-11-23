---
title: Cocos2d-x利用CCSpriteBatchNode提高渲染效率
tags:
  - CCSpriteBatchNode
id: 331
categories:
  - Cocos2D-X
date: 2012-11-14 07:14:51
---

以前写过一个游戏中的天气系统，今天有人说我的天气系统有点卡，不是很好用。我看下了他的代码，发现他改了我的代码，把CCSpriteBatchNode删除了，直接用addChild，每个sprite都是重新读取的贴图。

&nbsp;

我想说，同学呀，你把暴风雪所有特效开启后，你有没有注意下你的游戏场景显示了1000多渲染批次。这能不卡吗？

&nbsp;

今天刚好有点时间，我介绍下CCSpriteBatchNode，以及利用它优化游戏渲染效率。

在cocos2d-x 2.x 之后，大家都看到了左下角的FPS变成3行，多了两行数据。

最上面一行是指的当前场景的渲染批次。（简单理解为需要渲染多少个贴图出来）

中间一行是渲染每一帧需要的时间。

最下行就是大家熟悉的FPS。

&nbsp;

CCSpriteBatchNode介绍：

1、先说下渲染批次：这是游戏引擎中一个比较重要的优化指标，指的是一次渲染凋用。也就是说，渲染的次数越少，游戏的运行效率越高。

2、CCSpriteBatchNode就是cocos2d-x为了降低渲染批次而建立的一个专门管理精灵的类。

&nbsp;

举例介绍：

1、使用CCSprite创建1000个Icon.png到场景中，这样渲染批次就是1000（暂且不考虑其他的精灵）

[cpp]

for(int i = 0;i &lt; 1000;++i){
 int x = arc4random()%960;
 int y = arc4random()%640;
 CCSprite* testIcon = CCSprite::create(&quot;Icon.png&quot;);
 testIcon-&gt;setPosition( ccp(x, y) );
 this-&gt;addChild(testIcon);
}

[/cpp]

效果图如下：

[![](http://www.cocos2dev.com/wp-content/uploads/2012/11/IMG_0002.jpg "IMG_0002")](http://www.cocos2dev.com/wp-content/uploads/2012/11/IMG_0002.jpg)

上面的效果图可以看到，创建了1000个Icon到场景中，这是的FPS是22，渲染批次是1000次。

&nbsp;

2、使用CCSpriteBatchNode批量渲染，一次渲染就把所有的CCSprite绘制出来。大大降低渲染批次。

举例介绍：

1、使用CCSprite创建1000个Icon.png到场景中，但是这里利用了CCSpriteBatchNode批量渲染。这时的渲染批次、FPS如何呢？

[cpp]

CCSpriteBatchNode* batchNode = CCSpriteBatchNode::create(&quot;Icon.png&quot;, 1000);
batchNode-&gt;setPosition(CCPointZero);
this-&gt;addChild(batchNode);

for(int i = 0;i &lt; 1000;++i){
 int x = arc4random()%960;
 int y = arc4random()%640;
 CCSprite* testIcon = CCSprite::createWithTexture(batchNode-&gt;getTexture());
 testIcon-&gt;setPosition( ccp(x, y) );
 batchNode-&gt;addChild(testIcon);
}

[/cpp]

效果图如下：

[![](http://www.cocos2dev.com/wp-content/uploads/2012/11/IMG_0001.jpg "IMG_0001")](http://www.cocos2dev.com/wp-content/uploads/2012/11/IMG_0001.jpg)

上面的效果图可以看到，创建了1000个Icon到场景中，这是的FPS是39.9，渲染批次是1次。

&nbsp;

注：

1、
CCSpriteBatchNode::create(const char *fileImage);//利用贴图创建，默认子节点数量29.（数量不够时，系统会自己增加）
CCSpriteBatchNode* batchNode = CCSpriteBatchNode::create(const char *fileImage, unsigned int capacity);//利用贴图创建,并指定子节点数量

2、

使用CCSpriteBatchNode时，所使用的贴图必须是同一张图片，也不能指定精灵的深度。所有的精灵都必须在同一渲染层。

3、

但是项目中总不可能局限于一张贴图上，所以你可以把多张贴图合并成一张大贴图（合并的工具很多，我不介绍了）。所以利用合成后的大贴图创建一个CCSpriteBatchNode。
然后创建CCSprite的时候，设置贴图的区域就可以了。