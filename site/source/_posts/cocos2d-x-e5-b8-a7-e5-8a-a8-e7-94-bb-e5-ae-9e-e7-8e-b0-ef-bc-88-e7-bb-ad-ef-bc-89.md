---
title: cocos2d-x帧动画实现（续）
tags:
  - 动画
id: 74
categories:
  - Cocos2D-X
date: 2012-04-10 05:07:38
---

帧动画就是很多张png的序列图实现轮流播放产生动画效果。

那么首先我们要一套动画的序列图，没有图的可以看引擎例子里面的图。很多张图我们可以采用TP工具将它们压缩到一张png里面去，这样程序只需要读取一次就行了，提高效率。

![](http://hi.csdn.net/attachment/201111/3/0_132032178337K4.gif)

比如我将这里的6张图压成了一个png，TP会产生一个所有图的png和一个plist描述文件，plist很像xml，它描述了每一张图的位置，大小等信息。程序就是通过plist文件在合成的大png里面找到每一张图的。

合成的大图叫fei.png，对应的fei.plist。

里面的小图。叫 飞0001.png、飞0002.png、.........、飞0006.png

下面开始程序的创建

[cpp]

//创建cache
 CCSpriteFrameCache* cache = CCSpriteFrameCache::sharedSpriteFrameCache();
 char strPlist[64] = {0};
 char strPng[64] = {0};
 sprintf(strPlist,&quot;fei.plist&quot;);
 //sprintf(strPng,&quot;fei.pvr.ccz&quot;);
 sprintf(strPng,&quot;fei.png&quot;);
 cache-&gt;addSpriteFramesWithFile(strPlist, strPng);

 //创建动画每一帧，从cache里面读取
 CCMutableArray&lt;CCSpriteFrame*&gt;* animFrames = new CCMutableArray&lt;CCSpriteFrame*&gt;(6);

 char str[64] = {0};
 for(int i = 1; i &lt;= 6; i++)
 {
 sprintf(str, &quot;飞%04d.png&quot;, i);
 CCSpriteFrame* frame = cache-&gt;spriteFrameByName( str );
 animFrames-&gt;addObject(frame);
 }

 CCAnimation* animation = CCAnimation::animationWithFrames(animFrames,0.04f);

 CCRepeatForever* mFly=CCRepeatForever::actionWithAction( CCAnimate::actionWithAnimation(animation, false));
 animFrames-&gt;release();
 cache-&gt;removeSpriteFramesFromFile(strPlist);

[/cpp]

这里的mFly就是一个创建好的action。当然它是可以重复播放的。你要是只想播放一次。

那么替换成

[cpp]CCActionInterval* mFly=CCAnimate::actionWithAnimation(animation,true);[/cpp]

你可能看到了我里面注释了一行代码，就是我不是压缩成了png，而是压缩成了pvr.ccz。这种格式效率更高。

pvr是苹果自己支持的图片格式，但是比较占内存，压缩成ccz后就很小了。用法一样。