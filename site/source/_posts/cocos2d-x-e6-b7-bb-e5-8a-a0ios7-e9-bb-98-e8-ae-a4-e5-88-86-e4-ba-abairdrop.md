---
title: Cocos2d-x 添加iOS7默认分享/AirDrop
tags:
  - airdrop
  - cocos2dx
  - ios7
id: 530
categories:
  - Cocos2D-X
  - IPhone
date: 2014-03-22 14:39:55
---

[![ios7share](http://www.cocos2dev.com/wp-content/uploads/2014/03/ios7share.png)](http://www.cocos2dev.com/wp-content/uploads/2014/03/ios7share.png)

下午添加分享的时候，看着这么多第三方sdk，我还是喜欢ios7默认的分享功能，简洁大方。它也能显示你已安装的社交app。

下面我说下如何在cocos2dx里面使用。

下面是封装好的IOSShareManager.h/m ，这个是一个oc单例类，负责调用分享和保存rootViewController。代码很少，看注释就可以了明白了。

IOSShareManager.h
<pre class="lang:default decode:true">//
//  IOSShareManager.h
//  IOS7ShareSample
//
//  Created by LiuYanghui on 14-3-22.
//
//

#import &lt;Foundation/Foundation.h&gt;
#import &lt;UIKit/UIKit.h&gt;

@interface IOSShareManager : NSObject

+ (IOSShareManager *) getInstance;
- (void)share:(CGPoint)pos;

@property (nonatomic, readwrite, retain) id viewController;

@end</pre>
IOSShareManager.m
<pre class="lang:default decode:true">//
//  IOSShareManager.m
//  IOS7ShareSample
//
//  Created by LiuYanghui on 14-3-22.
//
//

#import "IOSShareManager.h"

@interface IOSShareManager()
@property (strong, nonatomic) UIPopoverController *activityPopover;
@end

@implementation IOSShareManager

+ (IOSShareManager *) getInstance
{
    static IOSShareManager* gameMgr = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&amp;onceToken, ^{
        gameMgr = [[self alloc] init];
    });
    return gameMgr;
}

- (void)share:(CGPoint)pos
{
    // 你可以先截屏保存到document下，这里的image读取document下面。
    UIImage *image = [UIImage imageNamed:@"HelloWorld.png"];
    NSString *mesg = @"You can add some description info here!";

    UIActivityViewController *activityViewController = [[UIActivityViewController alloc] initWithActivityItems:@[image, mesg] applicationActivities:nil];

    if ([[UIDevice currentDevice] userInterfaceIdiom] == UIUserInterfaceIdiomPhone) {
        //iPhone 从底部向上滑出view
        [self.viewController presentViewController:activityViewController animated:YES completion:nil];
    } else {
        //iPad, 弹出view
        if (![self.activityPopover isPopoverVisible]) {
            self.activityPopover = [[UIPopoverController alloc] initWithContentViewController:activityViewController];
            [self.activityPopover presentPopoverFromRect:CGRectMake(pos.x, pos.y, 0, 0) inView:((UIViewController *)self.viewController).view permittedArrowDirections:UIPopoverArrowDirectionAny animated:YES];
        } else {
            //Dismiss view，当重复点击时
            [self.activityPopover dismissPopoverAnimated:YES];
        }
    }
}

- (id)init
{
    self = [super init];
    if (self) {
        // init code here

    }
    return self;
}

- (void) dealloc
{
    [super dealloc];
}

@end</pre>
上面的是oc的类，下面添加中间层接口，供cpp调用。IOSShare.h/mm 就是我们需要添加的中间层类方法。

IOSShare.h
<pre class="lang:default decode:true">//
//  IOSShare.h
//  IOS7ShareSample
//
//  Created by LiuYanghui on 14-3-22.
//
//

#ifndef __IOS7ShareSample__IOSShare__
#define __IOS7ShareSample__IOSShare__

class IOSShare {
public:

/*! @brief share
 *
 * 显示分享view，如果是ipad，则从设置位置弹出view
 * @param posX
 * @param posY
 */
    static void share(float posX, float posY);
};

#endif /* defined(__IOS7ShareSample__IOSShare__) */</pre>
IOSShare.mm
<pre class="lang:default decode:true">//
//  IOSShare.cpp
//  IOS7ShareSample
//
//  Created by LiuYanghui on 14-3-22.
//
//

#include "IOSShare.h"
#import "IOSShareManager.h"

void IOSShare::share(float posX, float posY)
{
    [[IOSShareManager getInstance] share:CGPointMake(posX, posY)];
}</pre>
ok，下面就是如何使用。

第一步：设置IOSShareManager 的rootViewController。
请在AppController.mm，添加：#import "IOSShareManager.h", 并在下面函数中设置IOSShareManager 的rootViewController
<pre class="lang:default decode:true">- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions 
{
    // ....
    [[IOSShareManager getInstance] setViewController:viewController];
}</pre>
第二步：cpp中调用分享，参数坐标当在ipad下的时候，以该坐标为弹出点，弹出分享视图。
IOSShare::share(300, 300);