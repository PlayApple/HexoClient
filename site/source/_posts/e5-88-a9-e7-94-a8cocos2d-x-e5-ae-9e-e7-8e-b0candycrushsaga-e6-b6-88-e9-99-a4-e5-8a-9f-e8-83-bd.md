---
title: 利用cocos2d-x实现CandyCrushSaga消除功能
tags:
  - CandyCrushSaga
  - cocos2dx
id: 455
categories:
  - Cocos2D-X
date: 2013-11-06 03:08:27
---

[![2](http://www.cocos2dev.com/wp-content/uploads/2013/11/2.png)](http://www.cocos2dev.com/wp-content/uploads/2013/11/2.png)

昨天没事写了个三消玩玩。已经实现的功能：
1、连续3个及以上横竖消除方块；
2、连续4个及以上生成一个道具；（只做了消除一行或一列的条纹道具）

其他功能就没有实现了，下面我简单说下思路，希望给需要的朋友一点帮助。

[![1](http://www.cocos2dev.com/wp-content/uploads/2013/11/1.png)](http://www.cocos2dev.com/wp-content/uploads/2013/11/1.png)

GameScene：游戏场景
GameBox：游戏视图View。负责管理GameTitle
GameTile：可消除的方块
GameDefine：一些游戏的配置。

一、GameScene
1、首先GameScene初始化的时候，创建GameBox，并且锁住GameBox，阻止玩家操作界面上的方块。
2、当onEnterTransitionDidFinish之后，让GameBox check是否有消除对象。
3、GameScene的touch方法只负责GameTile的交换。每次交换之后，让GameBox check是否有消除对象。

二、GameTile
这里要说明下，GameTile是放置在GameBox上的每个方块，玩家交换GameTile实际上是交换的GameTile内部的sprite，GameTile本身不移动位置。
[cpp]

class GameTile: public Node

{
public:
 int x;
 int y;
 int value; // 1 - 6 6种方块
 int comboValue; // 1:横向消除道具, 2:竖向消除道具
 Sprite* sprite; // 方块的显示sprite,

public:
 GameTile(int ax,int ay);
 ~GameTile();
 bool nearTile(GameTile* other tile); // 是否相连
 void trade(GameTile* other tile); //交换gameTile，实际上是交换的sprite
 void changeComboTile(int orient); //变身成combo道具
 Point pixPosition(); //返回x，y对应的像素坐标，用于交换sprite
 void refreshDebugInfo();
};

[/cpp]

三、GameBox
1、首先生成所有的GameTitle，新生成的GameTile没有sprite。
2、GameScene onEnterTransitionDidFinish之后会调用GameBox check方法，会发现所有GameTitle为空，所以开始执行填充sprite。
3、在GameScene中用户交换gameTile之后，也会触发GameBox check，GameBox check检查是否有可消除对象，没有的话则通知GameScene还原交换的gameTile，有消除对象，则GameBox 消除对应的sprite，并填充新的sprite并执行check。

四、最后提供源代码：
代码是基于cocos2d-x-3.0alpha0版本的，请下载后放在projects目录运行。

[![3](http://www.cocos2dev.com/wp-content/uploads/2013/11/3.png)](http://www.cocos2dev.com/wp-content/uploads/2013/11/3.png)

&nbsp;

地址：[https://github.com/sunny-liu/GameCandy](https://github.com/sunny-liu/GameCandy)