---
title: cocos2d-x 实现按Home和待机键后，再进入游戏显示暂停界面
id: 96
categories:
  - Cocos2D-X
date: 2012-04-10 05:18:55
tags:
---

要实现按Home和待机键后，再进入游戏显示暂停界面，其实很简单的。

cocos2d已经实现了按Home和待机键后暂停游戏，当再进入游戏时直接调用了resume，恢复游戏，所以你看到的是再进去后马上接着退出前的画面继续。

现在我们要实现的是再次进入游戏后，不要马上继续游戏，而是显示我们自己的暂停界面，很多游戏都是这样设计的。

首先，看看如何暂停游戏和恢复游戏。

//暂停游戏

cocos2d::CCDirector::sharedDirector()-&gt;pause();

//恢复游戏

cocos2d::CCDirector::sharedDirector()-&gt;resume();

很简单吧，cocos2d暂停游戏时保存当前的状态。

实现代码知道了，现在就剩下在游戏中按Home和待机键，再次进入游戏时，先显示暂停UI，当按下继续按建后，调用resume，

在AppController.mm中我们可以看到这样的代码：

游戏中按下Home和待机键会调用改方法，里面其实就是调用了pause

- (void)applicationWillResignActive:(UIApplication *)application {

cocos2d::CCDirector::sharedDirector()-&gt;pause();

}

//再次进入后，会调用这个方法，里面实现显示一个暂停UI，当点下继续按钮时，才调用resume。

- (void)applicationDidBecomeActive:(UIApplication *)application {

cocos2d::CCDirector::sharedDirector()-&gt;resume();

}