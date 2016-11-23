---
title: iOS8 UILocalNotification 增加启动授权
tags:
  - iOS8
  - UILocalNotification
id: 633
categories:
  - IPhone
date: 2015-07-08 15:17:10
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！ 
原文地址: http://blog.csdn.net/cocos2der/article/details/46810357

好久没接入UILocalNotification了，今天接入时发现没有权限启动通知。 
错误如下：

<pre class="lang:default decode:true " >Attempting to schedule a local notification &lt;UIConcreteLocalNotification: 
0x174178540&gt;{fire date = (null), time zone = (null), repeat interval =
0, repeat count = UILocalNotificationInfiniteRepeatCount, next fire
a badge number but haven't received permission from the user to badge
the application</pre> 
看了下API，原来iOS8增加了启动授权，需要用户同意下才能注册通知。

添加如下代码：

<pre class="lang:default decode:true " >- (void)RegistNotificationSettings
{
    float sysVersion=[[UIDevice currentDevice]systemVersion].floatValue;
    if (sysVersion&gt;=8.0) {
        UIUserNotificationType type = UIUserNotificationTypeBadge | UIUserNotificationTypeAlert | UIUserNotificationTypeSound;
        UIUserNotificationSettings *setting = [UIUserNotificationSettings settingsForTypes:type categories:nil];
        [[UIApplication sharedApplication] registerUserNotificationSettings:setting];
    }
}</pre> 

你可以在注册通知之前调用，也可以在app启动时候调用

<pre class="lang:default decode:true " >- (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
{
    // todo ...

    [self RegistNotificationSettings];
    return YES;
}</pre> 

注意：只要用户对app授权过了，以后即使删除再次安装，也默认为授权了。