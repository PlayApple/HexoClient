---
title: cocos2d-x 根据png序列图创建animation
tags:
  - 动画
id: 84
categories:
  - Cocos2D-X
date: 2012-04-10 05:13:27
---

之前都是使用TP格式化swf来创建动画，今天遇到了个问题，我拿到的动画序列图是合成好的一张png。没有plist文件。悲催...

现在只好自己画区域创建动画。

先看看png序列图。我就随便拿一个来用用。(先说明哈，该图是我从一个android游戏里面抽出来的。无侵权之意。)

![](http://hi.csdn.net/attachment/201111/10/0_1320926852O3w3.gif)

[cpp]

CCTexture2D *playerRunTexture = CCTextureCache::sharedTextureCache()-&gt;addImage(&quot;player_run.png&quot;);
 CCMutableArray&lt;CCSpriteFrame*&gt;* animFrames = new CCMutableArray&lt;CCSpriteFrame*&gt;(11);
 for(int i=0;i&lt;8;i++){
 animFrames-&gt;addObject(CCSpriteFrame::frameWithTexture(playerRunTexture, cocos2d::CCRectMake(72*i, 0, 72, 72)));
 }
 //生成CCAnimation对象
 CCAnimation* animation = new CCAnimation();
 animation-&gt;initWithFrames(animFrames, 0.08f);
 animFrames-&gt;release();
 //创建动画
 CCAnimate *animate = CCAnimate::actionWithAnimation(animation, false);

 CCSprite* player=CCSprite::spriteWithSpriteFrame(CCSpriteFrame::frameWithTexture(playerRunTexture, cocos2d::CCRectMake(0, 0, 72, 72)));
 player-&gt;runAction(CCRepeatForever::actionWithAction(animate));
 player-&gt;setPosition(ccp(100,mWinSize.height*.5-45));
 this-&gt;addChild(player,zOrder++);

[/cpp]

代码很简单，循环里面只用到了序列图1到8，后面三张是另外一个动作的。

OK，就这样咯，看看咱的效果：

![](http://hi.csdn.net/attachment/201111/10/0_1320927301Zm6A.gif)![](http://hi.csdn.net/attachment/201111/10/0_1320927314QfF9.gif)