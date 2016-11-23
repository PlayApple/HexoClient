---
title: iOS平台添加Google Admob -2/2（Unity3D开发之八）
tags:
  - Admob
  - iOS
  - Unity3D
id: 572
categories:
  - Unity3D
date: 2014-08-07 06:09:38
---

[![admob](http://www.cocos2dev.com/wp-content/uploads/2014/08/admob.png)](http://www.cocos2dev.com/wp-content/uploads/2014/08/admob.png)

在[上一篇文章](http://www.cocos2dev.com/?p=567)中主要是编写了iOS Admob的接口实现。那么现在我们要做的事就是在unity中调用iOS Admob并展示。

一、实现Unity中对外接口，内部负责调用iOS Admob接口。
LHiOSAdmob.cs

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;
using System.Runtime.InteropServices;
using System.Linq;
using System.Collections.Generic;

public enum GADAdSize:int
{
	// iPhone and iPod Touch ad size. Typically 320x50.
	kGADAdSizeBanner = 1,

	// Taller version of kGADAdSizeBanner. Typically 320x100.
	kGADAdSizeLargeBanner,

	// Medium Rectangle size for the iPad (especially in a UISplitView's left pane). Typically 300x250.
	kGADAdSizeMediumRectangle,

	// Full Banner size for the iPad (especially in a UIPopoverController or in
	// UIModalPresentationFormSheet). Typically 468x60.
	kGADAdSizeFullBanner,

	// Leaderboard size for the iPad. Typically 728x90.
	kGADAdSizeLeaderboard,

	// Skyscraper size for the iPad. Mediation only. AdMob/Google does not offer this size. Typically
	// 120x600.
	kGADAdSizeSkyscraper,

	// An ad size that spans the full width of the application in portrait orientation. The height is
	// typically 50 pixels on an iPhone/iPod UI, and 90 pixels tall on an iPad UI.
	kGADAdSizeSmartBannerPortrait,

	// An ad size that spans the full width of the application in landscape orientation. The height is
	// typically 32 pixels on an iPhone/iPod UI, and 90 pixels tall on an iPad UI.
	kGADAdSizeSmartBannerLandscape
}

public class LHiOSAdmob : MonoBehaviour {
	[DllImport("__Internal")]
		private static extern void startRequestAdmob(string admobId, int adSizeId, float pixelX, float pixelY);
	[DllImport("__Internal")]
	private static extern void setAdmobHidden(bool isHidden);

	public static LHiOSAdmob Instance;
	private GADAdSize adSize;

	void Awake()
	{
		// singleton
		if (Instance != null)
		{
			Debug.LogError("Multiple instances of LHiOSAdmob");
		}
		Instance = this;
	}

	// Init google admob
	// It will request the admob after five seconds.
	// It's will auto show admob.
	public void InitAdmob(string admobId, GADAdSize size, Vector2 pos)
	{
		#if UNITY_IPHONE
		int adSizeId = (int)size;
		startRequestAdmob(admobId, adSizeId, pos.x, pos.y);
		#else
		Debug.Log("Admob only run on iPhone platform");
		#endif
	}

	// Set Admob BannerView is visible or not
	public void SetAdmobVisible(bool isVisible)
	{
		#if UNITY_IPHONE
		setAdmobHidden(!isVisible);
		#else
		Debug.Log("Admob only run on iPhone platform");
		#endif
	}

	// Use this for initialization
	void Start () {

	}

	// Update is called once per frame
	void Update () {

	}
}
</pre> 

这是一个单例接口类，内部负责调用iOS Admob接口，并开放对外接口。供其他c#文件调用。
注意：你可以制作一个Prefab，并包含该脚本。将该Prefab拖放到场景中。在需要显示admob的地方调用。

二、测试admob显示，也就是在你需要的地方。

TestAdmob.cs

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;

public class TestAdmob : MonoBehaviour {
	// Use this for initialization
	void Start () {
		LHiOSAdmob.Instance.InitAdmob("your admob id", GADAdSize.kGADAdSizeSmartBannerLandscape, new Vector2(0, 150));
	}

	// Update is called once per frame
	void Update () {

	}
}
</pre> 

ok, 现在可以Build iOS工程了。

注意：

Build的iOS工程，要添加对应的编译选项和iOS框架。

1、Other Linker Flags 添加 -ObjC

2、Xcode->Targets->Build Phases->Link Binary With Libraries 添加以下框架：
AdSupport
AudioToolbox
AVFoundation
CoreGraphics
MessageUI
StoreKit
SystemConfiguration