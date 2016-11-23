---
title: ios7内购、Game Center 实现 in-App Purchases & Game Center
tags:
  - Game Center
  - in-App Purchases
id: 514
categories:
  - IPhone
date: 2014-01-27 09:01:31
---

昨天使用ios7SDK build的时候，发现了几个warning，原来以前写的内购方法，有些在ios7下弃用了。今天改了下，顺便代码也发上。

PGStoreKitManager.h

<pre class="lang:default decode:true " >//
// PGStoreKitManager.h
// OCPhysicGame
//
// Created by LiuYanghui on 14-1-26.
// Copyright (c) 2014年 LiuYanghui. All rights reserved.
//

#import <Foundation/Foundation.h>
#import <GameKit/GameKit.h>
#import <StoreKit/StoreKit.h>

@class ViewController;
@interface PGStoreKitManager : NSObject<GKGameCenterControllerDelegate, SKPaymentTransactionObserver, SKProductsRequestDelegate>
{
UIAlertView *_loadingAlert;
BOOL _enableGameCenter;
}

@property (nonatomic, readwrite, strong) ViewController* viewController;
+ (PGStoreKitManager *)getInstance;

// game center ----------------------------------------
/**
登陆gamecenter，请先设置setViewController
*/
- (void)authenticateLocalPlayer;

/**
上传积分
*/
- (void)reportScore : (NSString*)identifier hiScore:(int64_t)score;

/**
上传成就
*/
- (void)reportAchievementIdentifier : (NSString*)identifier percentComplete:(float)percent;

/**
显示排行版
*/
- (void)showLeaderboard : (NSString*)leaderboard;

/**
显示成就
*/
- (void)showAchievements;
// iap ----------------------------------------
/**
初始化内消费
*/
- (void)initStoreKit;

/**
购买产品
*/
- (void)purchaseItem: (NSString*)identifier;
@end</pre> 

PGStoreKitManager.m

<pre class="lang:default decode:true " >//
//  PGStoreKitManager.m
//  OCPhysicGame
//
//  Created by LiuYanghui on 14-1-26.
//  Copyright (c) 2014年 LiuYanghui. All rights reserved.
//

#import "PGStoreKitManager.h"
#import "ViewController.h"

@implementation PGStoreKitManager

+ (PGStoreKitManager *)getInstance
{
    static PGStoreKitManager *mgr = nil;
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
        [self initData];
        return self;
    }
    return nil;
}

- (void)initData
{
    _enableGameCenter = NO;
    _viewController = nil;
}

- (void)showMessage:(NSString *)title Message:(NSString *)msg
{
    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:msg delegate:self cancelButtonTitle:@"确定" otherButtonTitles:nil, nil];
    [alert show];
}

- (void)showLoadingView:(NSString *)title
{
    _loadingAlert= [[UIAlertView alloc] initWithTitle:title message:@"" delegate:self cancelButtonTitle:nil otherButtonTitles:nil, nil];
    [_loadingAlert show];
}

- (void)removeLoadingView
{
    [_loadingAlert dismissWithClickedButtonIndex:0 animated:YES];
}

#pragma mark - GameCenter
- (void)authenticateLocalPlayer
{
    GKLocalPlayer* localPlayer = [GKLocalPlayer localPlayer];
    if ([localPlayer isAuthenticated] == NO) {
        localPlayer.authenticateHandler = ^(UIViewController *viewController,
          NSError *error) {
            if (error) {
                _enableGameCenter = NO;
            }else{
                _enableGameCenter = YES;
                if(viewController) {
                    [_viewController presentViewController:viewController animated:YES completion:nil];
                }
            }
        };
    }else{
        _enableGameCenter = YES;
    }
}

/**
 上传积分
 */
- (void)reportScore : (NSString*)identifier hiScore:(int64_t)score;
{
    if (score &lt; 0 || !_enableGameCenter)
		return;
	GKScore *scoreBoard = [[GKScore alloc] initWithLeaderboardIdentifier:identifier];
    scoreBoard.value = score;
    [GKScore reportScores:@[scoreBoard] withCompletionHandler:^(NSError *error) {
        if (error) {
            // handle error
        }
    }];
}

/**
 上传成就
 */
- (void)reportAchievementIdentifier : (NSString*)identifier percentComplete:(float)percent
{
    if (percent &lt; 0 || !_enableGameCenter)
		return;

    GKAchievement *achievement = [[GKAchievement alloc] initWithIdentifier: identifier];
    if (achievement){
		achievement.percentComplete = percent;
        [GKAchievement reportAchievements:@[achievement] withCompletionHandler:^(NSError *error) {
            if (error) {
                // handle error
            }
        }];
    }
}

/**
 显示排行版
 */
- (void)showLeaderboard : (NSString*)leaderboard
{
    if (!_enableGameCenter)
		return;
    GKGameCenterViewController *gameCenterViewController = [[GKGameCenterViewController alloc] init];
    gameCenterViewController.viewState = GKGameCenterViewControllerStateLeaderboards;
    gameCenterViewController.gameCenterDelegate = self;
    [_viewController presentViewController:gameCenterViewController animated:YES completion:nil];
}

/**
 显示成就
 */
- (void)showAchievements
{
    if (!_enableGameCenter)
		return;

    GKGameCenterViewController *gameCenterViewController = [[GKGameCenterViewController alloc] init];
    gameCenterViewController.viewState = GKGameCenterViewControllerStateAchievements;
    gameCenterViewController.gameCenterDelegate = self;
    [_viewController presentViewController:gameCenterViewController animated:YES completion:nil];
}

#pragma mark gameCenterViewController Close回调
- (void)gameCenterViewControllerDidFinish:(GKGameCenterViewController *)gameCenterViewController
{
    [_viewController dismissViewControllerAnimated:YES completion:nil];
}

//---------------------------------------------------------
#pragma mark - IAP
- (BOOL)canProcessPayments
{
    if ([SKPaymentQueue canMakePayments]) {
        return YES;
    } else {
        return NO;
    }
}

/**
 初始化内消费
 */
- (void)initStoreKit
{
    [[SKPaymentQueue defaultQueue] addTransactionObserver:self];
}

/**
 购买产品
 */
- (void)purchaseItem: (NSString *)identifier
{
    [self showLoadingView:@"Access Store..."];

    if (![self canProcessPayments]) {
        NSLog(@"1.失败--&gt;SKPaymentQueue canMakePayments NO");
        [self removeLoadingView];
        return;
    }
    NSLog(@"1.成功--&gt;请求产品信息...%@", identifier);

    // 使用请求商品信息式购买
    SKProductsRequest *request= [[SKProductsRequest alloc]
                                 initWithProductIdentifiers: [NSSet setWithObject: identifier]];
    request.delegate = self;
    [request start];
}

// SKProductsRequest 的回调
- (void)productsRequest:(SKProductsRequest *)request didReceiveResponse:(SKProductsResponse *)response
{
    NSArray *myProduct = response.products;

    if (myProduct.count == 0) {
        NSLog(@"2.失败--&gt;无法获取产品信息，购买失败。invalidProductIdentifiers = %@",response.invalidProductIdentifiers);
        [self removeLoadingView];
        return;
    }
    NSLog(@"2.成功--&gt;获取产品信息成功，正在购买...");
    SKPayment * payment = [SKPayment paymentWithProduct:myProduct[0]];
    [[SKPaymentQueue defaultQueue] addPayment:payment];
}

// SKPayment 的回调
- (void)paymentQueue:(SKPaymentQueue *)queue updatedTransactions:(NSArray *)transactions
{
    NSLog(@"3.成功--&gt;接收苹果购买数据，正在处理...");
    for (SKPaymentTransaction *transaction in transactions){
        switch (transaction.transactionState){
            case SKPaymentTransactionStatePurchased:
                [self completeTransaction:transaction];
                break;

            case SKPaymentTransactionStateFailed:
                [self failedTransaction:transaction];
                break;

            case SKPaymentTransactionStateRestored:
                [self restoreTransaction:transaction];
                break;

            default:
                break;
        }
    }
}

// 结束交易
- (void) completeTransaction: (SKPaymentTransaction*)transaction
{
    NSLog(@"4.成功--&gt;结束交易 SKPaymentTransactionStatePurchased");
    [self removeLoadingView];
	// 记录交易和提供产品 这两方法必须处理
    [self recordTransaction: transaction];
    [self provideContent: transaction.payment.productIdentifier];

    // 移除 transaction from the payment queue.
    [[SKPaymentQueue defaultQueue] finishTransaction: transaction];
}

// 重置交易
- (void) restoreTransaction: (SKPaymentTransaction*)transaction
{
    NSLog(@"4.成功--&gt;重置交易 SKPaymentTransactionStateRestored");
    [self recordTransaction: transaction];
    [self provideContent: transaction.originalTransaction.payment.productIdentifier];
    [[SKPaymentQueue defaultQueue] finishTransaction: transaction];
}

// 交易失败
- (void) failedTransaction: (SKPaymentTransaction*)transaction
{
    [self removeLoadingView];
    NSLog(@"4.成功--&gt;交易失败 SKPaymentTransactionStateRestored error.code:%d",(int)transaction.error.code);
    [[SKPaymentQueue defaultQueue] finishTransaction: transaction];
}

// 交易记录
- (void) recordTransaction: (SKPaymentTransaction*)transacation
{
    NSLog(@"4.成功--&gt;交易记录, 可以在此处存储记录");
}

// 提供产品
- (void) provideContent: (NSString*)identifier
{
    NSLog(@"4.成功--&gt;交易成功，请提供产品 identifier = %@", identifier);

    [self removeLoadingView];
    [self showMessage:@"Success" Message:@"You have successfully purchased."];
}

@end
</pre> 

注释已经写的很清楚了，再有不清楚的可以留言。