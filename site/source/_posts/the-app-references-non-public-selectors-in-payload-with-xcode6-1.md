---
title: The app references non-public selectors in payload With Xcode6.1
tags:
  - Archive
  - non-public selectors
id: 591
categories:
  - IPhone
date: 2014-10-26 03:07:02
---

今天上传appStore的时候出现The app references non-public selectors in payload错误，这个主要是Xcode升级到6.1导致的一个Xcode issue，下一个Xcode版本应该会解决。

[![non-public_selectors](http://www.cocos2dev.com/wp-content/uploads/2014/10/non-public_selectors.png)](http://www.cocos2dev.com/wp-content/uploads/2014/10/non-public_selectors.png)

目前如果你遇到这个问题，两个解决办法：

第一个办法，有点挫，使用Xcode6.01上传。（不推荐这个办法）

第二个办法，推荐：
1、你的Archives里面，该App一定存在以前的老版本Archive，你删除该App的所有Archive。
2、重新Archive一个新的发布版本，再次上传，就没有问题了。