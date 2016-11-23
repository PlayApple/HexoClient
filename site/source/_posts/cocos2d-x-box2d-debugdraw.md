---
title: Cocos2d-x Box2D debugDraw
tags:
  - Box2D
id: 62
categories:
  - Box2D
  - Cocos2D-X
date: 2012-04-10 04:54:47
---

最近想看看物理引擎效果，Box2d的DebugDraw，可以在游戏屏幕下绘制Box2d的各种对象信息， 让我们可以观察自己生成的b2body对象大小、位置、刚体类型等信息是否正确。

在Cocos2d-x下使用Box2d的DebugDraw，有一点需要注意，就是去掉游戏背景- -，一开始我以为DebugDraw是绘制在z轴最上方，所以调用DebugDraw但结果什么都没显示令我觉得很诡异，后来想到也许是z轴不是最上方导致被背景遮住了，一试，果真如此…

如何使用DebugDraw
Win32下, 使用DebugDraw，需要添加两个文件：GLES-Render.h和GLES-Render.cpp，如果找不到这两个文件，可以在cocos2d-x源代码tests的Box2d例子下找到。

接着，在代码中包含这个头文件：

[cpp]

#import &quot;GLES-Render.h&quot;

[/cpp]

然后，添加DebugDraw成员变量：

[cpp]GLESDebugDraw * m_debugDraw; [/cpp]

接着，在init方法中添加下面的代码：

[cpp]

m_debugDraw = new GLESDebugDraw( PTM_RATIO );
m_world-&gt;SetDebugDraw(m_debugDraw); //注册到Box2d的world对象里面
uint32 flags = 0;
flags += b2DebugDraw::e_shapeBit; //要绘制的信息，这里只绘制Box2d的Shape。
m_debugDraw-&gt;SetFlags(flags);

[/cpp]

接着，我们需要添加一个draw方法来绘制信息。这样基本上差不多了, 运行效果如上面的图片。

[cpp]

void BattleLayer::draw()
{
glDisable(GL_TEXTURE_2D);
glDisableClientState(GL_COLOR_ARRAY);
glDisableClientState(GL_TEXTURE_COORD_ARRAY);

m_world-&gt;DrawDebugData();

glEnable(GL_TEXTURE_2D);
glEnableClientState(GL_COLOR_ARRAY);
glEnableClientState(GL_TEXTURE_COORD_ARRAY);
}

[/cpp]

最后记得delete掉：

[cpp]

void BattleLayer::onExit()

{
delete m_world;
delete m_debugDraw;
}

[/cpp]