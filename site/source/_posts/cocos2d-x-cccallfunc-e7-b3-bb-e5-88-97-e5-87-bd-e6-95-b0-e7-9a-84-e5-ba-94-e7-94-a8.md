---
title: cocos2d-x CCCallFunc系列函数的应用
id: 68
categories:
  - Cocos2D-X
date: 2012-04-10 05:02:00
tags:
---

CCCallFunc作为一个CCAction可以用来调用自己的函数。其用处还是很多的。

1、用一个不带参数的函数给一个action创建回调功能。使用CCCallFunc

[cpp]

CCFiniteTimeAction* actions= CCSequence::actions(action,
 CCCallFunc::actionWithTarget(this,callfunc_selector(MyClass::callBackAnim)),NULL);

[/cpp]

这就创建了一个带回调函数的action，当这个action执行完毕后，就会执行callBackAnim函数。这里的callBackAnim是MyClass里面的不带参数的函数。最后要加上一个NULL，因为CCSequence相当于一个队列，它会按顺序执行里面的所有action，最后一个加NULL。表示到这里就没有了。

2、用一个带参数的函数给一个action创建回调功能。使用CCCallFuncDN

回调函数为callBackAnim(CCNode* sender,int myInt);

[cpp]CCFiniteTimeAction* actions=CCSequence::actions(action,CCCallFuncND::actionWithTarget(this, callfuncND_selector(MyClass::callBackAnim), (void*)mInt),NULL);[\cpp]

里面的参数含义和上面的一样，不同的是(void*)mInt这个是传给callBackAnim的参数。也许你会说callBackAnim有两个参数啊？的确是这样的，CCNode* sender就是谁run这个action谁就被当做sender传递进来。你也可以操作这个sender。

3、CCCallFuncD

回调函数为callBackAnim(CCNode* sender);

[cpp]

CCFiniteTimeAction* actions=CCSequence::actions(action,CCCallFuncND::actionWithTarget(this, callfuncND_selector(MyClass::callBackAnim)),NULL);

[/cpp]