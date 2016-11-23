---
title: cocos2d-x 使用action实现各种动画效果
tags:
  - 动画
id: 72
categories:
  - Cocos2D-X
date: 2012-04-10 05:06:10
---

做项目遇到了很多动画效果，基本大多数使用action实现的，当然也可以自己在每一帧的时候自己去实现，原理基本一样的。

1、使用CCMoveTo左右快速抖动。

[cpp]

CCPoint pointL=pointBg;
CCPoint pointR=pointBg;
pointL.x-=3;
pointR.x+=3;
CCMoveTo* moveLeft=CCMoveTo::actionWithDuration(0.08, pointL);
CCMoveTo* moveRight=CCMoveTo::actionWithDuration(0.08, pointR);
CCFiniteTimeAction* action= CCSequence::actions(moveLeft,moveRight,NULL);
CCActionInterval* actionShake=CCRepeatForever::actionWithAction((CCActionInterval*)action);
skillBg-&gt;stopAllActions();
skillBg-&gt;runAction(reSkillAnim);

[/cpp]

pointBg就是你需要左右抖动的sprite。

CCMoveTo就是移动到哪里去，参数是移动时间，目的坐标点。(CCMoveBy是在当前位置移动多少距离，有点区别。)

CCSequence前面就说了，你可以看作为执行一个action的队列，遇到NULL就结束了。

因为是要实现不同的左右移动，所以我将左右移动包装了一下，包装成一个CCRepeatForever的action，它是不停的重复播放这个action。

2、CCJumpTo从一个地方跳跃到另外一个地方。

[cpp]CCJumpTo* mJumpTo = CCJumpTo::actionWithDuration(0.2f, mToPosition, 30.0f, 1); [/cpp]

第一个参数是跳远的时间，第二个参数是跳跃的目的点，第三个参数是跳跃的最大高度，第四个参数是跳跃的次数。

3、CCDelayTime 可以用来作为暂停停多长时间。

[cpp]CCDelayTime *waiting=CCDelayTime::actionWithDuration(0.2f);  [/cpp]

暂停0.2秒。

4、CCScaleTo 缩小/放大到。(区别CCScaleBy，缩小/放大多少)

[cpp]CCScaleTo* scale=CCScaleTo::actionWithDuration(0.5, 0.2);  [/cpp]

第一个参数时间，第二次参数缩放倍数，=1正常大小，&gt;1放大，&lt;1缩小

5、CCRotateTo旋转角度。和上面一样。角度0-360。

6、CCWaves 和CCShaky3D

[cpp][/cpp]

CCActionInterval* waves = CCWaves::actionWithWaves(5, 20, true, false, ccg(15,10), 5);
CCActionInterval* shaky = CCShaky3D::actionWithRange(4, false, ccg(15,10), 5);

[\cpp]

CCWaves波浪效果，CCShaky3D震动效果。它是全屏执行，具体效果，自己跑下就看到了，截图看不到什么效果。