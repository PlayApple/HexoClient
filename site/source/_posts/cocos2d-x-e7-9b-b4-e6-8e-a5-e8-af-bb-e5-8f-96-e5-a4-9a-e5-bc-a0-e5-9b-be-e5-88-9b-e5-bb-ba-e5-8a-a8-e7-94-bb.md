---
title: cocos2d-x 直接读取多张图创建动画
tags:
  - 动画
id: 92
categories:
  - Cocos2D-X
date: 2012-04-10 05:17:23
---

以前都是使用TP压缩之后读取plist创建动画，昨天突然要读临时取几张图，其实也不麻烦。代码如下

[cpp]

CCAnimation* animation = CCAnimation::animation();

char frameName[100] = {0};

for( int i=1;i&lt;34;i++){

sprintf(frameName, &quot;demo%04d.png&quot;, i);

animation-&gt;addFrameWithFileName(frameName);

}

CCActionInterval*  action = CCAnimate::actionWithDuration(1.0f, animation, false);[/cpp]