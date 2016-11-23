---
title: 'cocos2d-x 调用CMMotionManager,获取三轴陀螺仪数据'
tags:
  - 陀螺仪
id: 98
categories:
  - Cocos2D-X
date: 2012-04-10 05:20:26
---

陀螺仪

从Iphong4开始，iphone4装载了陀螺仪。据乔布斯所介绍，该装置可以让iPhone4感知人体的移动方向，结合重力感应、加速感应装置可以给iPhone 4提供前所未有的完美游戏体验！

陀螺仪的作用简单点说就是可以跟踪位置变化，只要你在某个时刻得到了当前所在位置，然后只要陀螺仪一直在运行，根据数学计算，就可以知道你的行动轨迹。所以陀螺仪最常见的应用就是导航仪，在GPS没有信号时，通过陀螺仪的作用仍然能够继续精确导航。

在三维电子罗盘中就用到陀螺仪，不仅具有指南针功能，还可以实现倾角的测量，和陀螺仪类似，站在一个斜坡上，可以计算出这个斜坡的角度；有点像玩虚拟飞机的时候的飞机位置平衡仪（不知道那玩意是不是这样叫。呵呵）

这两个东西要比重力感应高级的多，也是未来手机应用中的主流，全新的控制方式，更有趣的应用，都会随之而诞生。

这是我找的一个陀螺仪的图，itunes上有个应用就是个陀螺仪，忘记叫撒了，需要的可以去找找看。介绍就这些了，具体的大家google下。

![](http://hi.csdn.net/attachment/201112/1/0_1322714539Zr98.gif)

Roll（左右倾斜）、Pitch（前后倾斜）、Yaw（左右摇摆），陀螺仪重要的三个数据

现在开始实现：

一。加载CoreMotion.framework

在工程的Frameworks目录下可以看到默认加载的库，没有CoreMotion.framework。

![](http://hi.csdn.net/attachment/201112/1/0_13227148763PlN.gif)

点击工程的TARGETS,点击右边的Build Phases，展开Link Binary With Libraries。里面看到你工程的加载库，默认没有CoreMotion.framework，这个截图是我已经加了的。

点击左下方的+号，添加我们要的framework。

![](http://hi.csdn.net/attachment/201112/1/0_1322715138yWWW.gif)

OK，加载完毕。

二、实现

在你需要MotionManager的类中加入头文件，

[cpp]

#include&lt;CoreMotion/CoreMotion.h&gt;

#import&lt;CoreFoundation/CoreFoundation.h&gt;

[/cpp]

注意，由于motionManager使用OC写的，所以把cpp改成mm后缀，头文件不要加到.h里面，在mm文件中加入上面引用的头文件。

初始化MotionManager：

//请在mm的头文件中申明一个void* m_motionManager;用来保存局部的motionManager对象，将它作为全局使用。

[cpp]

voidARGameLevel::initMotion(){

CMMotionManager *motionManager = [[CMMotionManageralloc] init];

motionManager.deviceMotionUpdateInterval =1.0/60.0;

if (motionManager.isDeviceMotionAvailable) {

[motionManagerstartDeviceMotionUpdates];

}

m_motionManager=(void*)motionManager;

schedule(schedule_selector(ARGameLevel::updateMotion));

}

&amp;nbsp;

//不停输出yaw roll pitch的值。

voidARGameLevel::updateMotion(){

CMMotionManager* temMotion=(CMMotionManager*)m_motionManager;

CMDeviceMotion *currentDeviceMotion = temMotion.deviceMotion;

CMAttitude *currentAttitude = currentDeviceMotion.attitude;

float yaw = roundf((float)(CC_RADIANS_TO_DEGREES(currentAttitude.yaw)));

float roll = roundf((float)(CC_RADIANS_TO_DEGREES(currentAttitude.roll)));

float pitch = roundf((float)(CC_RADIANS_TO_DEGREES(currentAttitude.pitch)));

}

[/cpp]

上面的yaw roll pitch就是我们需要的值了，它会实时随着你手机的状态改变而改变。

大家可能会想，这玩意好像没有什么用处。我玩意在游戏中还没怎么使用，但以后肯定会非常普及的，我业余做的游戏使用AR虚拟现实技术，里面角色在现实世界的位置就是靠陀螺仪定位的。