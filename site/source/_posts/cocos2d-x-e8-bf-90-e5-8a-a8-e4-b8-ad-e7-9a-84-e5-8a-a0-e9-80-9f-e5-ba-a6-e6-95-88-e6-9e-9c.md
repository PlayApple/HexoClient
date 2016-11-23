---
title: cocos2d-x 运动中的加速度效果
id: 108
categories:
  - Cocos2D-X
date: 2012-04-10 05:24:04
tags:
---

在实现运动中，我们常常需要实现一些加速度或者减速度的效果，cocos2d-x引擎为我们提供了相应的实现接口，这样我们就不用再用原来的公式计算方法来实现加减速度的效果

Ease系列的方法改变了运动的速度，但是并没有改变总体时间，如果整个的action持续5秒钟，那么整个的时间仍然会持续5秒钟。

这些action可以被分成3类：

In actions: action开始的时候加速

Out actions: action结束的时候加速

InOut actions: action开始，结束的时候加速

第一个参数为要加减速度的动作，第二个为加减的速率

还有一些特殊的缓冲公式继承了进来

1.指数缓冲

[![](http://www.cocos2dev.com/wp-content/uploads/2012/04/11.png "1")](http://www.cocos2dev.com/wp-content/uploads/2012/04/11.png)

EaseExponentialIn

EaseExponentialOut

EaseExponentialInOut

2.赛因缓冲

[![](http://www.cocos2dev.com/wp-content/uploads/2012/04/21.png "2")](http://www.cocos2dev.com/wp-content/uploads/2012/04/21.png)

EaseSineIn

EaseSineOut

EaseSineInOut

3.弹性缓冲

[![](http://www.cocos2dev.com/wp-content/uploads/2012/04/3.png "3")](http://www.cocos2dev.com/wp-content/uploads/2012/04/3.png)

EaseElasticIn

EaseElasticOut

EaseElasticInOut

4.跳跃缓冲

[![](http://www.cocos2dev.com/wp-content/uploads/2012/04/4.png "4")](http://www.cocos2dev.com/wp-content/uploads/2012/04/4.png)

EaseBounceIn

EaseBounceOut

EaseBounceInOut

5.回震缓冲

[![](http://www.cocos2dev.com/wp-content/uploads/2012/04/5.png "5")](http://www.cocos2dev.com/wp-content/uploads/2012/04/5.png)

EaseBackIn

EaseBackOut

EaseBackInOut

另外还可以设置速度的倍数

通过把动作定义为CCSpeed并改变速度，使用setSpeed将速度按参数的倍数变大或者缩小,这样可以手动实现加减速度