---
title: cocos2d-x 使用setTextureRect的问题
id: 76
categories:
  - Cocos2D-X
date: 2012-04-10 05:08:13
tags:
---

前天项目中，我有一个ccsprite,初始化成了一张图。代码中我要更换成另外一个图。但是这个图是使用TP合并后，我已经加载到了cache中。

后来采取了这样的办法，

[cpp]

CCSpriteFrame* mFrameImg=cache-&gt;spriteFrameByName(&quot;icon.png&quot;);

mImg-&gt;setTextureRect(mFrameImg-&gt;getRect());

[/cpp]

这样确实能够替换图片，但是它是从原来合并的大图里面截取的区域大小，要是刚好这张图合并时被旋转了，那么得到的图也是一个被旋转之后的。

后来使用了个很猥琐的方法，将mImg移除，然后在初始化，添加到场景中。再不然就要是重新生成plist。让合成的图没有旋转。

怎么做还没有很好的办法