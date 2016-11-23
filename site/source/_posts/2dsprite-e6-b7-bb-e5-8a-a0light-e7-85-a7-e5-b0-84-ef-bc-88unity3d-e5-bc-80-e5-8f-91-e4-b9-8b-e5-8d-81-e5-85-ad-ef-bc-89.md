---
title: 2DSprite添加Light照射（Unity3D开发之十六）
tags:
  - 2DSprite
  - Light
  - Material
  - Unity3D
id: 618
categories:
  - Unity3D
date: 2015-05-06 04:18:49
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！ 
原文地址: http://blog.csdn.net/cocos2der/article/details/45534245

今天项目中需要用到黑夜灯光照射，由于普通的2DSprite使用的材质是不接收光反射的。所以我们必须修改Sprite Renderer的材质。

第一步，建立工程

下面是我建立的工程，你会发现Point light对2DSprite没有产生任何作用。
[![2DSpriteAddLight1](http://www.cocos2dev.com/wp-content/uploads/2015/05/2DSpriteAddLight1.png)](http://www.cocos2dev.com/wp-content/uploads/2015/05/2DSpriteAddLight1.png)

第二步，修改Sprite Material, 让其接收光反射
[![2DSpriteAddLight2](http://www.cocos2dev.com/wp-content/uploads/2015/05/2DSpriteAddLight2.png)](http://www.cocos2dev.com/wp-content/uploads/2015/05/2DSpriteAddLight2.png)
1\. Assets->Create->Meterial ，并修改Shader为Sprite/Diffuse 
2\. 选中2DSprite，修改其Sprite Renderer的Material为1步中创建的Meterial。 
3\. 拖动Point light到合适位置。

你会发现玛丽已经可以接收光发射了，你也可以设置光源的光颜色。 
下面是效果图：（真实比这个效果好，gif录制帧率低） 
[![2DSpriteAddLight3](http://www.cocos2dev.com/wp-content/uploads/2015/05/2DSpriteAddLight3.gif)](http://www.cocos2dev.com/wp-content/uploads/2015/05/2DSpriteAddLight3.gif)