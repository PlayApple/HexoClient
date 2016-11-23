---
title: iOS平台添加Google Admob -1/2（Unity3D开发之七）
tags:
  - Admob
  - iOS
  - Unity3D
id: 567
categories:
  - Unity3D
date: 2014-08-07 04:01:17
---

[![admob](http://www.cocos2dev.com/wp-content/uploads/2014/08/admob.png)](http://www.cocos2dev.com/wp-content/uploads/2014/08/admob.png)

Unity调用iOS还是非常简单的，晚上空闲时间写了unity在IOS平台加载Google Admob。需要的朋友可以看下。

一、首先编写Admob的iOS的代码。
请去https://apps.admob.com下载iOS Admob SDK, 并注册app id。如果这一步不熟悉，请自行Google，网上应该很多。

1、IOSAdmob类，主要实现Admob的创建和加载
IOSAdmob.h

<pre class="lang:default decode:true " >//
//  IOSAdmob.h
//  IOSAdmob
//
//  Created by LiuYanghui on 14-8-4.
//  Copyright (c) 2014年 LiuYanghui. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;
#import "GADBannerView.h"
#import "GADBannerViewDelegate.h"

@interface IOSAdmob : NSObject&lt;GADBannerViewDelegate&gt;

- (void)startRequestAdmob:(NSString*)admobId AdSize:(GADAdSize)size Position:(CGPoint)pos;
- (void)setAdmobHidden:(BOOL)isHidden;

@end
</pre> 

IOSAdmob.m

<pre class="lang:default decode:true " >//
//  IOSAdmob.m
//  IOSAdmob
//
//  Created by LiuYanghui on 14-8-4.
//  Copyright (c) 2014年 LiuYanghui. All rights reserved.
//

#import "IOSAdmob.h"

@interface IOSAdmob ()
    @property (nonatomic) BOOL isHidden;
    @property (nonatomic) CGRect adRect;
    @property (nonatomic, readonly, strong) UIViewController* rootViewController;
    @property (nonatomic, readonly, strong) GADBannerView* adBannerView;
@end

@implementation IOSAdmob

- (id) init
{
	self = [super init];
	if(self != nil)
	{
        _isHidden = NO;
        _adRect = CGRectZero;
        _rootViewController = [[[UIApplication sharedApplication] keyWindow] rootViewController];
        _adBannerView = nil;
	}
	return self;
}

- (void) dealloc
{
    [_rootViewController release];
    [_adBannerView release];

	[super dealloc];
}

- (void) startRequestAdmob:(NSString*)admobId AdSize:(GADAdSize)size Position:(CGPoint)pos
{
    if (_adBannerView) { return; }

    _adBannerView = [[GADBannerView alloc] initWithAdSize:size];
//    if ([[UIDevice currentDevice] userInterfaceIdiom] == UIUserInterfaceIdiomPhone) {
//        
//    }else {
//
//    }

    _adRect = CGRectMake(pos.x, pos.y, _adBannerView.frame.size.width, _adBannerView.frame.size.height);
    _adBannerView.adUnitID = admobId;
    _adBannerView.delegate = self;
    [_adBannerView setRootViewController:_rootViewController];
    _adBannerView.frame = _adRect;
    [_rootViewController.view addSubview:_adBannerView];

    double delayInSeconds = 5.0;
    dispatch_time_t popTime = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(delayInSeconds * NSEC_PER_SEC));
    dispatch_after(popTime, dispatch_get_main_queue(), ^(void){
        NSLog(@"Retrying to load request");
        [_adBannerView loadRequest:[self createRequest]];
    });
}

- (void)setAdmobHidden:(BOOL)isHidden
{
    _isHidden = isHidden;
    if (_adBannerView) {
        [_adBannerView setHidden:_isHidden];
    }
    //        [UIView animateWithDuration:0.5 animations:^ {
    //            _adBannerView.frame = _adRect;
    //        }];
}

#pragma mark GADRequest generation

// Here we're creating a simple GADRequest and whitelisting the simulator
// and two devices for test ads. You should request test ads during development
// to avoid generating invalid impressions and clicks.
- (GADRequest *)createRequest {
    GADRequest *request = [GADRequest request];
    return request;
}

#pragma mark GADBannerViewDelegate impl

// Since we've received an ad, let's go ahead and set the frame to display it.
- (void)adViewDidReceiveAd:(GADBannerView *)adView {
    NSLog(@"Received ad");
    _adBannerView.frame = _adRect;
    [_adBannerView setHidden:_isHidden];
}

- (void)adView:(GADBannerView *)view
didFailToReceiveAdWithError:(GADRequestError *)error {
    NSLog(@"Failed to receive ad with error: %@", [error localizedFailureReason]);
    double delayInSeconds = 5.0;
    dispatch_time_t popTime = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(delayInSeconds * NSEC_PER_SEC));
    dispatch_after(popTime, dispatch_get_main_queue(), ^(void){
        NSLog(@"Retrying to load request");
        [_adBannerView loadRequest:[self createRequest]];
    });
}

@end
</pre> 

二、Admob对外单例类接口
IOSAdmobManager.h

<pre class="lang:default decode:true " >//
//  IOSAdmobManager.h
//  IOSAdmob
//
//  Created by LiuYanghui on 14-8-4.
//  Copyright (c) 2014年 LiuYanghui. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;
#import &lt;UIKit/UIKit.h&gt;

@interface IOSAdmobManager : NSObject

+ (IOSAdmobManager *)getInstance;

- (void)startRequestAdmob:(NSString*)admobId AdSizeId:(NSInteger)adSizeId Position:(CGPoint)pos;
- (void)setAdmobHidden:(BOOL)isHidden;

@end
</pre> 

IOSAdmobManager.m

<pre class="lang:default decode:true " >//
//  IOSAdmobManager.m
//  IOSAdmob
//
//  Created by LiuYanghui on 14-8-4.
//  Copyright (c) 2014年 LiuYanghui. All rights reserved.
//

#import "IOSAdmobManager.h"
#import "IOSAdmob.h"

@interface IOSAdmobManager ()
@property (nonatomic, readonly, strong) IOSAdmob* admob;
@end

@implementation IOSAdmobManager

+ (IOSAdmobManager *)getInstance
{
    static IOSAdmobManager *mgr = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&amp;onceToken, ^{
        mgr = [[self alloc] init];
    });
    return mgr;
}

- (id)init
{
    self = [super init];
    if (self) {
        _admob = [[IOSAdmob alloc] init];
        return self;
    }
    return nil;
}

- (void)startRequestAdmob:(NSString*)admobId AdSizeId:(NSInteger)adSizeId Position:(CGPoint)pos
{
    switch (adSizeId) {
        case 1:
            [_admob startRequestAdmob:admobId AdSize:kGADAdSizeBanner Position:pos];
            break;

        case 2:
            [_admob startRequestAdmob:admobId AdSize:kGADAdSizeLargeBanner Position:pos];
            break;

        case 3:
            [_admob startRequestAdmob:admobId AdSize:kGADAdSizeMediumRectangle Position:pos];
            break;

        case 4:
            [_admob startRequestAdmob:admobId AdSize:kGADAdSizeFullBanner Position:pos];
            break;

        case 5:
            [_admob startRequestAdmob:admobId AdSize:kGADAdSizeLeaderboard Position:pos];
            break;

        case 6:
            [_admob startRequestAdmob:admobId AdSize:kGADAdSizeSkyscraper Position:pos];
            break;

        case 7:
            [_admob startRequestAdmob:admobId AdSize:kGADAdSizeSmartBannerPortrait Position:pos];
            break;

        case 8:
            [_admob startRequestAdmob:admobId AdSize:kGADAdSizeSmartBannerLandscape Position:pos];
            break;

        default:
            [_admob startRequestAdmob:admobId AdSize:kGADAdSizeBanner Position:pos];
            break;
    }
}

- (void)setAdmobHidden:(BOOL)isHidden
{
    [_admob setAdmobHidden:isHidden];
}

@end
</pre> 

三、为第二步中的单例接口注册C函数接口，提供给Unity使用
LHAdmob.mm

<pre class="lang:default decode:true " >//
//  LHAdmob.mm
//  IOSAdmob
//
//  Created by LiuYanghui on 14-8-4.
//  Copyright (c) 2014年 LiuYanghui. All rights reserved.
//

#import "IOSAdmobManager.h"

extern "C" {
    void startRequestAdmob(const char* admobId, int adSizeId, float pixelX, float pixelY)
    {
        [[IOSAdmobManager getInstance] startRequestAdmob:[NSString stringWithUTF8String:admobId] AdSizeId:adSizeId Position:CGPointMake(pixelX, pixelY)];
    }

    void setAdmobHidden(bool isHidden)
    {
        [[IOSAdmobManager getInstance] setAdmobHidden:isHidden];
    }
}</pre> 

OK，到这里已经完成了Admob的创建和Unity接口编写。

注意：
1、Unity导出的iOS工程默认没有使用arc，所以以上代码全部未使用arc。
Xcode->Targets->Build Settings->Objective-C Automatic Reference Counting 设置NO，即关闭arc。

2、针对上诉接口创建什么类型的xCode工程 ？
你可以创建任意的Xcode工程，编写以上代码。也可以创建iOS->Framework & Library->Cocos Touch Static Library 静态库工程。

3、以上代码如何加到Unity？
第一个方法：直接将以上代码文件复制到Unity->Assets/Plugins/iOS 文件夹中，该文件是Unity指定的插件文件。
第二个方法：如果你创建的是静态库工程，Xcode->Product->Archive 编译出.a静态库文件，将.a文件复制到Unity->Assets/Plugins/iOS 文件夹中。