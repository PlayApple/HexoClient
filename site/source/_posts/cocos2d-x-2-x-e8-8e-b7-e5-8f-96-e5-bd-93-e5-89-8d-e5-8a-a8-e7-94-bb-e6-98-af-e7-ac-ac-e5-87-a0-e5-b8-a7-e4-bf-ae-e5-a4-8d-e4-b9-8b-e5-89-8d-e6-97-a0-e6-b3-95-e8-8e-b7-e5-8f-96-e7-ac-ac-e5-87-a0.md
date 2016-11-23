---
title: ' cocos2d-x 2.x 获取当前动画是第几帧 (修复之前无法获取第几帧问题)'
tags:
  - cocos2d-x
id: 391
categories:
  - Cocos2D-X
date: 2013-03-27 13:31:11
---

cocos2d-x 2.x 获取当前动画是第几帧 (修复之前无法获取第几帧问题)
有些人跟我QQ留言说 [《cocos2d-x获取当前动画是第几帧》](http://www.cocos2dev.com/?p=80)里面的方法已经不能对比到第几帧了，我看了下代码，找到了这个

[cpp]

CCSpriteFrame* CCSprite::displayFrame(void)
{
 return CCSpriteFrame::createWithTexture(m_pobTexture,
 CC_RECT_POINTS_TO_PIXELS(m_obRect),
 m_bRectRotated,
 CC_POINT_POINTS_TO_PIXELS(m_obUnflippedOffsetPositionFromCenter),
 CC_SIZE_POINTS_TO_PIXELS(m_obContentSize));
}

[/cpp]

大家看到了吧，到这里估计有人看出来问什么对比不到第几帧了吧。

当你调用displayFrame的时候，它重新生成了一个新的CCSpriteFrame对象给你，你拿着它和原来的作地址对比，肯定不相同，所以为什么不能找到相同的。

&nbsp;

那么是不是就没有办法了呢？

想一下，相同帧不就是贴图相同吗？所以看下Texture，果然看到这个

[cpp]

GLuint CCTexture2D::getName()
{
 return m_uName;
}

[/cpp]

返回当前Texture的Name，这个name是GLuint，你可以看成它的ID，每个Texture都是唯一的ID。

所以把之前的对比方法稍微修改下就可以了。

下面是修改好之后的

[cpp]

int currentAnimIndex = 0; //精灵当前播放的是第几帧
 for(int i = 0; i &lt; 5; i++)
 {
 //5张图5帧
 GLuint tID = prite-&gt;getTexture()-&gt;getName();
 CCSpriteFrame* animFrame = (CCSpriteFrame*)anim-&gt;getAnimation()-&gt;getFrames()-&gt;objectAtIndex(i);
 if(tID == animFrame-&gt;getTexture()-&gt;getName()){
 //这个i返回的只是一个索引,如果帧数是从1开始计算就要+1
 currentAnimIndex = i+1;
 }
 }

[/cpp]

现在可以获取到第几帧了，解决起来也不是很难，希望大家遇到问题，可以自己尝试这去找出原因，知道了原因就有解决的办法。