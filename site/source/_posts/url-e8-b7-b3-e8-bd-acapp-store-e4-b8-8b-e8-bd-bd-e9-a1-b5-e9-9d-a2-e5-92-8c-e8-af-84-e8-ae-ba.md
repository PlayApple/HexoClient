---
title: URL跳转App Store下载页面和评论
tags:
  - appstore
id: 475
categories:
  - Code
date: 2013-11-12 09:35:41
---

<div>[objc]&lt;/pre&gt;
//跳转到应用页面
NSString* urlStr1 = [NSString stringWithFormat:@&quot;http://itunes.apple.com/cn/app/id%d&quot;,appid];
[[UIApplication sharedApplication] openURL:[NSURL urlWithString:urlStr1]];

//跳转到评价页面
NSString *urlStr2 = [NSString stringWithFormat: @&quot;itms-apps://ax.itunes.apple.com/WebObjects/MZStore.woa/wa/viewContentsUserReviews?type=Purple+Software&amp;id;=%d&quot;, appid ];
[[UIApplication sharedApplication] openURL:[NSURL urlWithString:urlStr2]];
&lt;pre&gt;
[/objc]

</div>