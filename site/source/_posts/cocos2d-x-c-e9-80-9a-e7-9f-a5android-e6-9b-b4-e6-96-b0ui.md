---
title: cocos2d-x C++通知Android更新UI
id: 155
categories:
  - Android
  - Cocos2D-X
date: 2012-04-26 04:33:38
tags:
---

今天遇到一个问题，就是移植到android上的游戏在主界面显示广告，进入游戏有隐藏广告。

看看我实现的方法。

1、游戏启动时在java这边的xml里面放了一个view放置广告。在oncreate的时候初始化广告代码。

2、当进入游戏后，c++通过JNI通知java（前面我有介绍如果使用JNI），隐藏广告。

3、java这边隐藏广告：

主线程中更新UI方法，

[cpp]private Handler mHandler = new Handler(){
@Override
public void handleMessage(Message msg) {
switch (msg.what) {
case UPDATE_UI: {
//TODO 这里处理隐藏广告 adView.setVisibility(View.INVisible);
break;
}
default:
break;
}
}
}[/cpp]

当java收到通知要隐藏广告时，发出Message，通知更新UI。

[cpp]void hideAdView(){

mHandler.sendEmptyMessageDelayed(UPDATE_UI, 0);

}[/cpp]

注意：不要直接更新UI，必须在主线程中更新，不然会crash。