---
title: XCode5添加新建类模板（Cocos2dx Template Class for Scene or Layer）
tags:
  - Xcode
  - 文件模板
id: 505
categories:
  - Cocos2D-X
  - IPhone
date: 2013-12-27 05:17:41
---

因为常用cocos2dx开发游戏，所以每次新建cpp类文件的时候，XCode默认生成的cpp类基本就是一个空文件，每次都要重复实现cocos2dx的基本代码。很麻烦。（虽然我每次都是修改了HelloWorld文件，新建的文件直接copy HelloWorld内容。感觉还是挺麻烦的。）

今天就修改了下类模板，添加了2个cocos2dx的类模板，下面是新建文件的时候，是不是多出了2个类模板。

[![QQ20131227-1](http://www.cocos2dev.com/wp-content/uploads/2013/12/QQ20131227-1.png)](http://www.cocos2dev.com/wp-content/uploads/2013/12/QQ20131227-1.png)

下面我就说下怎么添加xcode类模板，很简单的。

1、打开Xcode的类模板目录地址：/Applications/Xcode.app/Contents/Developer/Library/Xcode/Templates/File Templates

2、打开里面C and C++文件，就会看到Xcode的cpp类模板文件。复制一个C++ Class.xctemplate文件夹取名：2dx Scene Class.xctemplate，放在C and C++目录下。

3、修改TemplateInfo.plist（主要修改创建文件时候的描述介绍）

打开2dx Scene Class.xctemplate/TemplateInfo.plist,   修改Description的值为：A C++ class for Cocos2d-x Scene.

4、修改___FILEBASENAME___.h

打开2dx Scene Class.xctemplate/___FILEBASENAME___.h，修改成以下内容：
<pre class="lang:default decode:true">//
// ___FILENAME___
// ___PROJECTNAME___
//
// Created by ___FULLUSERNAME___ on ___DATE___.
//___COPYRIGHT___
//

#ifndef _____PROJECTNAMEASIDENTIFIER________FILEBASENAMEASIDENTIFIER_____
#define _____PROJECTNAMEASIDENTIFIER________FILEBASENAMEASIDENTIFIER_____

#include "cocos2d.h"
USING_NS_CC;

class ___FILEBASENAMEASIDENTIFIER___ : public Layer
{
public:
___FILEBASENAMEASIDENTIFIER___();
~___FILEBASENAMEASIDENTIFIER___();
static Scene* createScene();
virtual bool init();
CREATE_FUNC(___FILEBASENAMEASIDENTIFIER___);

private:
void initData();
};

#endif /* defined(_____PROJECTNAMEASIDENTIFIER________FILEBASENAMEASIDENTIFIER_____) */</pre>
5、修改___FILEBASENAME___.cpp

打开2dx Scene Class.xctemplate/___FILEBASENAME___.cpp，修改成以下内容：
<pre class="lang:default decode:true">//
// ___FILENAME___
// ___PROJECTNAME___
//
// Created by ___FULLUSERNAME___ on ___DATE___.
//___COPYRIGHT___
//

#include "___FILEBASENAME___.h"
___FILEBASENAMEASIDENTIFIER___::___FILEBASENAMEASIDENTIFIER___()
{

}

___FILEBASENAMEASIDENTIFIER___::~___FILEBASENAMEASIDENTIFIER___()
{

}

Scene* ___FILEBASENAMEASIDENTIFIER___::createScene()
{
auto scene = Scene::create();
auto layer = ___FILEBASENAMEASIDENTIFIER___::create();
scene-&gt;addChild(layer);
return scene;
}

bool ___FILEBASENAMEASIDENTIFIER___::init()
{
if ( !Layer::init() )
{
return false;
}
initData();
return true;
}

#pragma mark - initData
void ___FILEBASENAMEASIDENTIFIER___::initData()
{
Size visibleSize = Director::getInstance()-&gt;getVisibleSize();

// add your codes here...

}</pre>
ok, 这样就完成了一个类模板的添加。

现在在XCode里面新建一个2dx Scene类看看。是不是已经有默认内容了。
<pre class="lang:default decode:true">//
// MyScene.h
// MagicDemo
//
// Created by LiuYanghui on 13-12-27.
//
//

#ifndef __MagicDemo__MyScene__
#define __MagicDemo__MyScene__

#include "cocos2d.h"
USING_NS_CC;

class MyScene : public Layer
{
public:
MyScene();
~MyScene();
static Scene* createScene();
virtual bool init();
CREATE_FUNC(MyScene);

private:
void initData();
};

#endif /* defined(__MagicDemo__MyScene__) */</pre>
<pre class="lang:default decode:true">//
// MyScene.cpp
// MagicDemo
//
// Created by LiuYanghui on 13-12-27.
//
//

#include "MyScene.h"
MyScene::MyScene()
{

}

MyScene::~MyScene()
{

}

Scene* MyScene::createScene()
{
auto scene = Scene::create();
auto layer = MyScene::create();
scene-&amp;gt;addChild(layer);
return scene;
}

bool MyScene::init()
{
if ( !Layer::init() )
{
return false;
}
initData();
return true;
}

#pragma mark - initData
void MyScene::initData()
{
Size visibleSize = Director::getInstance()-&amp;gt;getVisibleSize();

// add your codes here...

}</pre>
对应的你可以添加Layer的类模板。方法一样，我就不重复了。

关于文件模板里面的一个预定义变量，介绍下：

[![macro_file](http://www.cocos2dev.com/wp-content/uploads/2013/12/macro_file.jpg)](http://www.cocos2dev.com/wp-content/uploads/2013/12/macro_file.jpg)

ok，还有很多有趣的功能，可以大家自己探索。比如工程模板，插件等等。