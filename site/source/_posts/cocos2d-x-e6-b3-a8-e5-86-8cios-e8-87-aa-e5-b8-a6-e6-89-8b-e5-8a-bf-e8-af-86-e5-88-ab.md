---
title: cocos2d-x 注册iOS自带手势识别
tags:
  - 手势
id: 86
categories:
  - Cocos2D-X
date: 2012-04-10 05:15:26
---

现在项目中有涉及到手势识别的，其实我们可以直接利用ios自带的手势识别系统帮我们来识别。

看看实现：

我们要实现手势的类的头文件：

[cpp]

#include&quot;GTGestureProtocol.h&quot;

#include&quot;RootViewController.h&quot;

#include&quot;FMGBGameData.h&quot;

class  GTLevelMenu :publiccocos2d::CCLayer,publicGTGestureProtocol{

private:

virtualvoid GTGestureTrigger(GTGestureDir dir);

};

[/cpp]

在cpp中init方法中将自己传到RootViewController中。

[cpp]

RootViewController *root= (RootViewController*)(FMGBGameData::sharedFMGBGameData()-&gt;appRootViewController);

root.delegate=this;[/cpp]

使用的时候：

[cpp]

voidGTLevelMenu::GTGestureTrigger(GTGestureDir dir)

{

//dir就是下面枚举的几个方向

}

[/cpp]

不相关的代码我就不列出来了。

这里是一个layer，我想实现在这个layer上面实现手势监听。

GTGestureDir是一个代理，给iOS手势识别使用。GTGestureDir的实现：

[cpp]

typedefenum GTGestureDir

{

GTGestureDirNONE = 0,

GTGestureDirUP,

GTGestureDirDOWN,

GTGestureDirLEFT,

GTGestureDirRIGHT,

}GTGestureDir;

class GTGestureProtocol

{

public:

virtualvoid GTGestureTrigger(GTGestureDir dir){};

};

#endif

[/cpp]

下面修改如何从ios手势识别中返回结果：

在iOS目录下修改RootViewController.h。加入我们的代理类

[cpp]

//

//

//  Created by Yanghui Liu on 11-10-14.

//  Copyright company 2011年. All rights reserved.

//

#import&lt;UIKit/UIKit.h&gt;

&amp;nbsp;

#include&quot;GTGestureProtocol.h&quot;

@interface RootViewController :UIViewController {

GTGestureProtocol * delegate_;

&amp;nbsp;

}

@property (nonatomic,assign)GTGestureProtocol * delegate;

@end

&amp;nbsp;

&amp;nbsp;

在RootViewController.mm中加入下面的手势识别：

&amp;nbsp;

-(void)handleGesture:(UISwipeGestureRecognizer *)recognizer {

if (recognizer.direction==UISwipeGestureRecognizerDirectionDown) {

if (delegate_) {

delegate_-&gt;GTGestureTrigger(GTGestureDirDOWN);

}

}

elseif(recognizer.direction==UISwipeGestureRecognizerDirectionUp) {

if (delegate_) {

delegate_-&gt;GTGestureTrigger(GTGestureDirUP);

}

}

elseif(recognizer.direction==UISwipeGestureRecognizerDirectionLeft) {

if (delegate_) {

delegate_-&gt;GTGestureTrigger(GTGestureDirLEFT);

}

&amp;nbsp;

}

elseif(recognizer.direction==UISwipeGestureRecognizerDirectionRight) {

if (delegate_) {

delegate_-&gt;GTGestureTrigger(GTGestureDirRIGHT);

}

&amp;nbsp;

}

}

[/cpp]

最后还有一个单例返回的类没实现：

头文件

[cpp]

class FMGBGameData :publicCCObject {

public:

virtualbool init(void);

virtual ~FMGBGameData(void);

FMGBGameData(void);

/**单例FMGameApiRequest */

staticFMGBGameData* sharedFMGBGameData(void);

};

[/cpp]

&nbsp;

单例实现cpp

[cpp]

staticFMGBGameDatas_sharedData;

staticbool bFirstRun =true;

FMGBGameData::~FMGBGameData(void) {

}

FMGBGameData::FMGBGameData(){

}

FMGBGameData*FMGBGameData::sharedFMGBGameData(void) {

if (bFirstRun) {

s_sharedData.init();

bFirstRun =false;

}

return &amp;s_sharedData;

}

[/cpp]