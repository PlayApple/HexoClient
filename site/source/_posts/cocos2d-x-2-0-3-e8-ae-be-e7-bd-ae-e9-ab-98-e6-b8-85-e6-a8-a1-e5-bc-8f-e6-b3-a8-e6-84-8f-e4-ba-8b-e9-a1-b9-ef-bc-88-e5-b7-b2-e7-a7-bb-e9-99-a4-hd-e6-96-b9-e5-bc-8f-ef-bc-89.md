---
title: cocos2d-x 2.0.3 设置高清模式注意事项（已移除-hd方式）
tags:
  - hd
  - Retina
id: 304
categories:
  - Cocos2D-X
date: 2012-10-15 06:06:17
---

在cocos2d-x 2.x版本中，iphone设备设置Retina高清模式已经不是原来的在高清图后面加-hd后缀了。

2.x以前开启高清模式之后，底层会在addImage的时候自动在图片名称后面加-hd，优先加载-hd的图片，没有-hd的话，在加载原始图。（ipad是-ipad，ipad高清是-ipadhd）。

&nbsp;

2.0.3已经移除了这个模式（好像是2.0.2就改了），2.x之后将文件管理写成了一个共享单例类:CCFileUtils::sharedFileUtils();

&nbsp;

在加载图片的时候，会根据图片名称优先在getResourceDirectory()这里找资源，没有的话去resource目录下找。

&nbsp;

比如：程序开始时候，我们设置

CCFileUtils::sharedFileUtils()-&gt;setResourceDirectory("HD");

&nbsp;

那么所有的加载图片都会优先在 resource/HD 这个目录下面找，没有的话在resource目录找。

&nbsp;

所以在开启Retina高清模式的时候就不能用以前的方式把高清图加后缀-hd，而是应该在resource目录下，新建一个目录专门放高清图。

&nbsp;

在AppDelegate中，开启高清模式，并设置优先寻找图片目录为resource/HD

pDirector-&gt;enableRetinaDisplay(true);

CCFileUtils::sharedFileUtils()-&gt;setResourceDirectory("HD");

然后把高清的图放在resource/HD的目录下就可以了，不需要加后缀-hd。

&nbsp;

在这里也可以获取设备信息，根据不同设备设置不同的目录。当然如果不想把高清图放在resource也是可以的。

比如：CCFileUtils::sharedFileUtils()-&gt;setResourceDirectory("../HD");

这样HD应该是和resource处于同级目录下。