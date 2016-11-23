---
title: IOS LocalNotification 本地推送（添加、显示、移除）
tags:
  - UILocalNotification
id: 473
categories:
  - Code
date: 2013-11-12 09:28:40
---

[objc]

//第一步：创建本地推送
- (void)registUILocalNotification:(UIApplication *)application {
 // 创建一个本地推送
 UILocalNotification *notification = [[UILocalNotification alloc] init];

 //设置172800秒之后
 NSDate *pushDate = [NSDate dateWithTimeIntervalSinceNow:172800];

 if (notification != nil) {//判断系统是否支持本地通知
 // 设置推送时间
 notification.fireDate = pushDate;

 // 设置时区
 notification.timeZone = [NSTimeZone defaultTimeZone];

 // 设置重复间隔
 notification.repeatInterval = kCFCalendarUnitDay;

 // 推送声音
 notification.soundName = UILocalNotificationDefaultSoundName;

 // 推送内容
 notification.alertBody = @&quot;帮助他人回答问题可以赢取双倍金币哦&quot;;

 // 解锁按钮文字
 notification.alertAction = @&quot;挑战一下&quot;;

 // 显示在icon上的红色圈中的数子
 notification.applicationIconBadgeNumber = 1;

 // 设置userinfo 方便在之后需要撤销的时候使用
 NSDictionary *info = [NSDictionary dictionaryWithObject:@&quot;name&quot;forKey:@&quot;key&quot;];
 notification.userInfo = info;

 //添加推送到UIApplication
 [[UIApplication sharedApplication] scheduleLocalNotification:notification];
 [notification release];
 }
}

//第二步：接收本地推送
- (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification*)notification{
 // 显示推送内容（按需求是否显示）
 UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@&quot;提示&quot; message:notification.alertBody delegate:nil cancelButtonTitle:@&quot;确定&quot; otherButtonTitles:nil];
 [alert show];
 [alert release];

 // 图标上的数字减1
 application.applicationIconBadgeNumber -= 1;
}

//第三步：解除本地推送
- (void)removeUILocalNotification
{
 // 获得 UIApplication
 UIApplication *app = [UIApplication sharedApplication];

 //获取本地推送数组
 NSArray *localArray = [app scheduledLocalNotifications];

 //声明本地通知对象
 UILocalNotification *localNotification;
 if (localArray) {
 for (UILocalNotification *noti in localArray) {
 NSDictionary *dict = noti.userInfo;
 if (dict) {
 NSString *inKey = [dict objectForKey:@&quot;key&quot;];
 if ([inKey isEqualToString:@&quot;对应的key值&quot;]) {
 if (localNotification){
 [localNotification release];
 localNotification = nil;
 }
 localNotification = [noti retain];
 break;
 }
 }
 }

 //判断是否找到已经存在的相同key的推送
 if (!localNotification) {
 //不存在初始化
 localNotification = [[UILocalNotification alloc] init];
 }

 if (localNotification) {
 //不推送 取消推送
 [app cancelLocalNotification:localNotification];
 [localNotification release];
 return;
 }
 }
}

[/objc]