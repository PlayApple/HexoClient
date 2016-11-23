---
title: cocos2d-x 添加本地通知UILocalNotification
tags:
  - UILocalNotification
id: 120
categories:
  - Cocos2D-X
date: 2012-04-10 05:28:04
---

本地通知UILocalNotification现在被大量用在游戏设计中，目的在于召回用户。一个合理的通知提示，或许能让用户重新回到你的游戏中，那么如何在cocos2d-x中添加本地通知呢？

其实很简单，打开ios目录下的AppController.mm文件，在application函数最下方添加如下代码，最后结果如下：

[cpp]

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

 // Override point for customization after application launch.

// Add the view controller's view to the window and display.

 //........

 application.applicationIconBadgeNumber = 0;//应用程序右上角的数字=0（消失）
 [[UIApplication sharedApplication] cancelAllLocalNotifications];//取消所有的通知
 //------通知；
 UILocalNotification *notification=[[UILocalNotification alloc] init];
 if (notification!=nil) {//判断系统是否支持本地通知
 NSDate *now=[NSDate new];
 // notification.fireDate=[NSDate dateWithTimeIntervalSinceNow:kCFCalendarUnitDay];//本次开启立即执行的周期
 notification.fireDate=[now addTimeInterval:15];//本次开启立即执行的周期
 notification.repeatInterval=kCFCalendarUnitMinute;//循环通知的周期
 notification.timeZone=[NSTimeZone defaultTimeZone];
 notification.alertBody=@&quot;猴哥，要吃饭啦！&quot;;//弹出的提示信息
 notification.applicationIconBadgeNumber=1; //应用程序的右上角小数字
 notification.soundName= UILocalNotificationDefaultSoundName;//本地化通知的声音
 notification.alertAction = NSLocalizedString(@&quot;去吃饭&quot;, nil); //弹出的提示框按钮
 [[UIApplication sharedApplication] scheduleLocalNotification:notification];
 }
 //本地通知 end
 return YES;
}

[/cpp]

这里方便看效果，我把首次触发的时间写成15秒，这样程序运行后，按Home键退到主屏幕，等15秒就可以看到效果了。

&nbsp;

![](http://my.csdn.net/uploads/201204/09/1333939926_1152.PNG) ![](http://my.csdn.net/uploads/201204/09/1333939952_3162.PNG)

要同时启动多个通知，只需要注册多个就行了。