---
title: cocos2d-x 使用UIWebView加载网页
tags:
  - UIWebView
id: 248
categories:
  - Cocos2D-X
date: 2012-06-13 01:23:47
---

前段时间项目中要微博授权登陆，使用的是web登陆方式。所以要在cocos2d-x中显示网页。所以就将UIWebView用进来了。

[![](http://www.cocos2dev.com/wp-content/uploads/2012/06/1.png "1")](http://www.cocos2dev.com/wp-content/uploads/2012/06/1.png)

实现上讲也不是很难，你开打AppController.mm，你可以看到cocos2d-x的场景是被加载在一个EAGLView上面。所以我只需要取到EAGLView，在上面加一个UIView，UIView上面就可以放我的UIWebView。

实现：FMUIWebViewBridge.h

[cpp]

#import &lt;Foundation/Foundation.h&gt;

#import &lt;CoreLocation/CoreLocation.h&gt;

#import &lt;UIKit/UIKit.h&gt;

&amp;nbsp;

#import &quot;FMLayerWebView.h&quot;

&amp;nbsp;

@interface FMUIWebViewBridge : NSObject&lt;UIWebViewDelegate,UIAlertViewDelegate&gt;{

FMLayerWebView * mLayerWebView;

UIView    *mView;

UIWebView *mWebView;

UIToolbar *mToolbar;

UIBarButtonItem *mBackButton;

}

&amp;nbsp;

-(void) setLayerWebView : (FMLayerWebView*) iLayerWebView URLString:(const char*) urlString;

-(void) backClicked:(id)sender;

&amp;nbsp;

@end

[/cpp]

FMUIWebViewBridge.mm

[cpp]

//

//  FMUIWebViewBridge.cpp

//  WebViewDemo

//

//  Created by Yanghui Liu on 12-6-5.

//  Copyright (c) 2012年 FMinutes company. All rights reserved.

//

#import &quot;FMUIWebViewBridge.h&quot;

#import &quot;EAGLView.h&quot;

@implementation FMUIWebViewBridge

- (id)init{

self = [super init];

if (self) {

// init code here.

}

return self;

}

- (void)dealloc{

[mBackButton release];

[mToolbar release];

[mWebView release];

[mView release];

[super dealloc];

}

&amp;nbsp;

-(void) setLayerWebView : (FMLayerWebView*) iLayerWebView URLString:(const char*) urlString{

mLayerWebView = iLayerWebView;

cocos2d::CCSize size = mLayerWebView-&gt; getContentSize();

mView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, size.width , size.height)];

// create webView

//Bottom size

int wBottomMargin = size.height*0.10;

int wWebViewHeight = size.height - wBottomMargin;

mWebView = [[UIWebView alloc] initWithFrame:CGRectMake(0, 0, size.width, wWebViewHeight)];

mWebView.delegate = self;

NSString *urlBase = [NSString stringWithCString:urlString encoding:NSUTF8StringEncoding];

[mWebView loadRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:urlBase ]]];

[mWebView setUserInteractionEnabled:NO]; //don't let the user scroll while things are

//create a tool bar for the bottom of the screen to hold the back button

mToolbar = [UIToolbar new];

[mToolbar setFrame:CGRectMake(0, wWebViewHeight, size.width, wBottomMargin)];

mToolbar.barStyle = UIBarStyleBlackOpaque;

//Create a button

mBackButton = [[UIBarButtonItem alloc] initWithTitle:@&quot;Back&quot;

style: UIBarButtonItemStyleDone

target: self

action:@selector(backClicked:)];

//[backButton setBounds:CGRectMake(0.0, 0.0, 95.0, 34.0)];

[mToolbar setItems:[NSArray arrayWithObjects:mBackButton,nil] animated:YES];

[mView addSubview:mToolbar];

//[mToolbar release];

&amp;nbsp;

// add the webView to the view

[mView addSubview:mWebView];

[[EAGLView sharedEGLView] addSubview:mView];

}

&amp;nbsp;

- (void)webViewDidStartLoad:(UIWebView *)thisWebView {

}

&amp;nbsp;

- (void)webViewDidFinishLoad:(UIWebView *)thisWebView{

[mWebView setUserInteractionEnabled:YES];

mLayerWebView-&gt;webViewDidFinishLoad();

}

&amp;nbsp;

- (void)webView:(UIWebView *)thisWebView didFailLoadWithError:(NSError *)error {

if ([error code] != -999 &amp;&amp; error != NULL) { //error -999 happens when the user clicks on something before it's done loading.

&amp;nbsp;

UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@&quot;Network Error&quot; message:@&quot;Unable to load the page. Please keep network connection.&quot;

delegate:self cancelButtonTitle:nil otherButtonTitles:@&quot;OK&quot;, nil];

[alert show];

[alert release];

&amp;nbsp;

}

}

&amp;nbsp;

-(void) backClicked:(id)sender {

mWebView.delegate = nil; //keep the webview from firing off any extra messages

//remove items from the Superview...just to make sure they're gone

[mToolbar removeFromSuperview];

[mWebView removeFromSuperview];

[mView removeFromSuperview];

mLayerWebView-&gt;onBackbuttonClick();

}

@end

[/cpp]

因为这些都是OC代码，所以文件后缀使用mm，为了和C++代码混编。代码很简答，应该看得懂，我就不解释了。

下面就是封装这个oc代码，

FMLayerWebView.h

[cpp]

//

//  FMLayerWebView.h

//  WebViewDemo

//

//  Created by Yanghui Liu on 12-6-5.

//  Copyright (c) 2012年 FMinutes company. All rights reserved.

//

&amp;nbsp;

#ifndef WebViewDemo_FMLayerWebView_h

#define WebViewDemo_FMLayerWebView_h

&amp;nbsp;

#include &quot;CCCommon.h&quot;

#include &quot;cocos2d.h&quot;

USING_NS_CC;

class FMLayerWebView : public CCLayer{

public:

FMLayerWebView();

~FMLayerWebView();

virtual bool init();

LAYER_NODE_FUNC(FMLayerWebView);

void webViewDidFinishLoad();

void onBackbuttonClick();

private:

int mWebViewLoadCounter;

};

&amp;nbsp;

#endif
&lt;div&gt;&lt;/div&gt;
[/cpp]

FMLayerWebView.mm

[cpp]

//

//  FMLayerWebView.mm

//  WebViewDemo

//

//  Created by Yanghui Liu on 12-6-5.

//  Copyright (c) 2012年 FMinutes company. All rights reserved.

//

&amp;nbsp;

#include &quot;FMLayerWebView.h&quot;

#include &quot;FMUIWebViewBridge.h&quot;

&amp;nbsp;

static FMUIWebViewBridge *g_FMUIWebViewBridge=nil;

&amp;nbsp;

FMLayerWebView::FMLayerWebView(){

}

&amp;nbsp;

FMLayerWebView::~FMLayerWebView(){

[g_FMUIWebViewBridge release];

}

&amp;nbsp;

void FMLayerWebView::webViewDidFinishLoad(){

}

&amp;nbsp;

void FMLayerWebView::onBackbuttonClick(){

this-&gt;removeFromParentAndCleanup(true);

}

&amp;nbsp;

bool FMLayerWebView::init(){

if ( !CCLayer::init() ){

return false;

}

g_FMUIWebViewBridge = [[FMUIWebViewBridge alloc] init];

[g_FMUIWebViewBridge setLayerWebView : this URLString:&quot;http://www.cocos2dev.com&quot;];

return true;

}

[/cpp]

OK ,这样就完成了。

调用方法：

[cpp]

FMLayerWebView* web = FMLayerWebView::node();

web-&gt;setPosition(CCPointZero);

addChild(web);

[/cpp]

&nbsp;