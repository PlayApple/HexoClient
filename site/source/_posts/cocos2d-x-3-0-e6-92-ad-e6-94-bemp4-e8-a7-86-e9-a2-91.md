---
title: cocos2d-x 3.0 播放MP4视频
tags:
  - cocos2d-x
  - mp4
id: 545
categories:
  - Cocos2D-X
date: 2014-05-05 14:24:12
---

很久以前写的一个2dx播放MP4视频的教材，有网友反映已经不能用了，今天晚上写了个简单的2dx播放视频类。使用的是cocos2dx 3.0。

[![cocos2dx_mp4](http://www.cocos2dev.com/wp-content/uploads/2014/05/cocos2dx_mp4.png)](http://www.cocos2dev.com/wp-content/uploads/2014/05/cocos2dx_mp4.png)

类说明：
LHVideoPlayerImplCpp.h/mm  // cocos2dx中使用的播放MP4接口
LHVideoPlayerImpl.h/m      // videoPlayer的oc接口
LHVideoPlayer.h/m          // videoPlayer的实现，调用MPMoviePlayerController播放MP4
LHVideoOverlayView.h/m     // videoPlayer的上层操作层，有跳过影片按钮。

我就功能点而言，介绍其中的两个类。

第一个是LHVideoPlayerImplCpp.h/mm文件，这个是负责给2dx调用的。该类有两个静态方法：
<pre class="lang:default decode:true " >class LHVideoPlayerImplCpp {
public:
    /**
     * 开始播放MP4视频
     * name 视频名称，不要带后缀".mp4"。(比如文件是test.mp4, 那么name就传"test")
     * frameRect 视频显示的区域。全屏(Rect(0, 0, visibleSize.width, visibleSize.height))
     */
    static void playMP4WithName(const char* name, cocos2d::Rect frameRect);

    /**
     * 设置跳过影片按钮title，默认无跳过影片功能
     */
    static void setSkipTitle(const char* title);
};</pre>
第二个是LHVideoPlayer.h/m，这个是负责播放MP4。
1、这个方法是播放MP4。注释很清楚。
<pre class="lang:default decode:true " >- (void)playMP4WithName: (NSString *)name VideoFrame:(CGRect)rect
{
    UIWindow *keyWindow = [[UIApplication sharedApplication] keyWindow];

    // 获取视频文件的名称
    NSString *url = [[NSBundle mainBundle]pathForResource:name ofType:@"mp4"];
    // 初始化player
    _player = [[MPMoviePlayerController alloc] initWithContentURL:[NSURL fileURLWithPath:url]];
    [keyWindow.rootViewController.view addSubview: [_player view]];

    // 设置player样式
    [_player setControlStyle: MPMovieControlStyleNone];
    [[_player view] setFrame: rect];

    // 当MP4完成播放的回调
    [[NSNotificationCenter defaultCenter]
     addObserver:self selector:@selector(movieFinishedCallback:)
     name:MPMoviePlayerPlaybackDidFinishNotification object:_player];

    // 开始播放影片
    [_player play];

    // 上层操作层
    _videoOverlayView = [ [LHVideoOverlayView alloc] initWithFrame: rect];
    [keyWindow.rootViewController.view addSubview: _videoOverlayView];
}</pre>
2、这个方法是播放结束之后，移除播放view。
<pre class="lang:default decode:true " >- (void)removePlayer:(MPMoviePlayerController*)player
{
    [[NSNotificationCenter defaultCenter] removeObserver:self name:MPMoviePlayerPlaybackDidFinishNotification object:player];

    [player.view removeFromSuperview];
    [_player release];
    _player = nil;

    [_videoOverlayView removeFromSuperview];
    [_videoOverlayView release];
    _videoOverlayView = nil;
}</pre>
3、播放完成，通知外界。playerPlayFinished 这个方法是空的，没有通知外界。我看了下，感觉没什么需要，所以没加。

使用方法：

1、导入头文件
#include "LHVideoPlayerImplCpp.h"

2、开始调用接口，假设你要播放的是“loading.mp4”
Size visibleSize = Director::getInstance()->getVisibleSize();
LHVideoPlayerImplCpp::playMP4WithName("loading", Rect(0, 0, visibleSize.width, visibleSize.height));
LHVideoPlayerImplCpp::setSkipTitle("Skip");

3、影片结束之后，会自动移除视图。

git地址：[https://github.com/sunny-liu/Code/tree/master/cocos2dx_3.0_mp4](https://github.com/sunny-liu/Code/tree/master/cocos2dx_3.0_mp4)