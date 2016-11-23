---
title: cocos2d-x获取当前动画是第几帧
tags:
  - 动画
id: 80
categories:
  - Cocos2D-X
date: 2012-04-10 05:11:10
---

这段时间新项目涉及到很多animation，有时要知道当前动画播放到了第几帧，那么如何获取是第几帧呢？

有一个很龊的方法，就是根据时间判断，因为每一帧的时间我们是预先定了的，所以自己设置schedule多少时间之后开始执行。

还有一个好点的办法，就是真的获取到是第几帧：
假设有一个CCAnimation* anim，由5张图组成。
将这个anim包装成CCAnimate* animate。使用的时侯sprite-&gt;runAction(animate)，播放动画。

这里我们来获取第几帧：

[cpp]
int currentAnimIndex = 0; //精灵当前播放的是第几帧
for(int i = 0; i &lt; 5; i++)
{
//5张图5帧
if(prite-&gt;displayedFrame() == anim-&gt;getFrames()-&gt;getObjectAtIndex(i))
{
//这个i返回的只是一个索引,如果帧数是从1开始计算就要+1
currentAnimIndex = i+1;
}
}
[/cpp]

prite-&gt;displayedFrame()获取的是当前精灵动画所显示的帧精灵指(CCSpriteFrame).

anim-&gt;getFrames()获得的是整个动画所保存的帧精灵指针数组(CCSpriteFrame)

其实我们创建animation时所添加进去的图片文件都会转化成CCSpriteFrame保存起来。

animation-&gt;getFrames()-&gt;getObjectAtIndex(i)获取的是animation中帧精灵数组中某个索引的元素,这个元素也是CCSpriteFrame.

获取的原理就是，我通过sprite-&gt;displayedFrame()获得我当前精灵所播放的CCSpriteFrame,然后去跟我的动画数组(CCSpriteFrame数组)去做比较,取得对应元素在数组中的下标从而获取当前精灵播放的是第几帧。