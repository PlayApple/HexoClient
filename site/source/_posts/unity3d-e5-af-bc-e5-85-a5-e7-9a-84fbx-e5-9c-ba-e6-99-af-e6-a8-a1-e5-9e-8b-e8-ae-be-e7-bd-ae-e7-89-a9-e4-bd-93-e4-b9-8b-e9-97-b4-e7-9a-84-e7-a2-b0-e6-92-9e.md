---
title: Unity3D导入的FBX场景模型设置物体之间的碰撞
tags:
  - Unity3D
id: 339
categories:
  - Unity3D
date: 2012-11-29 10:06:05
---

导入的场景文件，发现人物会穿墙，也就是场景没有碰撞。

&nbsp;

那么如何添加mesh之间的碰撞呢？最后发现挺简单的。

&nbsp;

1、在Project中选中你的场景模型

2、在Inspector中勾选 Generate Colliders

3、Apply即可生成模型的mesh collider

&nbsp;

[![](http://www.cocos2dev.com/wp-content/uploads/2012/11/11.jpg "1")](http://www.cocos2dev.com/wp-content/uploads/2012/11/11.jpg)