---
title: No value was provided for the parameter ‘appIdName’ when provisioning
tags:
  - certificate
  - Provisioning
id: 373
categories:
  - IPhone
date: 2013-01-16 02:14:15
---

Xcode 自动提交certificate requests的时候，需要寻找一个“Wildcard” 的app Id，如果你丢了这个（一般是自动创建的），那么xcode的自动请求就会失败。解决起来也很简单。

&nbsp;

1、登陆到Developer。

&nbsp;

2、选中certificates,把你创建的certificates撤销了（Revoke）

&nbsp;

3、选中App IDs，创建一个名为Wildcard的app id，suffix敲入*，记住只有一个 * ，不带其他的。

&nbsp;

4、重新在xcode上再来一次生成Provisioning。
（不撤销certificates，好像也可以，我第二次做的时候好像没有撤销，呵呵）

&nbsp;