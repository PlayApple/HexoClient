---
title: App Store 评分代码
id: 118
categories:
  - IPhone
date: 2012-04-10 05:27:12
tags:
---

很多用户用了好软件后忘记或嫌麻烦而不去 App Store 进行打分评星，其实我们可以在应用中加入打分按钮，点击后直接跳转到 App Store 的评分界面。

App Store 上评论的链接地址

itms-apps://ax.itunes.apple.com/WebObjects/MZStore.woa/wa/viewContentsUserReviews?type=Purple+Software&amp;id = appID

所以可以用这段代码

[cpp]

NSString *str = [NSString stringWithFormat: @&quot;itms-apps://ax.itunes.apple.com/WebObjects/MZStore.woa/wa/viewContentsUserReviews?type=Purple+Software&amp;id=%d&quot;, m_appleID ];
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:str]];

[/cpp]