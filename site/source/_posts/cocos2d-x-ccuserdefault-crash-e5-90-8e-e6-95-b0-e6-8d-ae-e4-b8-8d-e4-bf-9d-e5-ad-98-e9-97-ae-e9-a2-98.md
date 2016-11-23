---
title: cocos2d-x CCUserDefault crash后数据不保存问题
tags:
  - CCUserDefault
id: 116
categories:
  - Cocos2D-X
date: 2012-04-10 05:26:40
---

cocos2d-x下面的CCUserDefault可以用来保存一些游戏数据和配置信息，和NSUserDefault用法是一样的。如果你还不明白的它的用法，可以看看这篇文章。

但是新版的cocos2d-x对CCUserDefault改动了一些，以前是只要setValue时就会保存数据到文件。现在的版本是调用setValue时并没有保存数据，而是保存在内存中，当CCUserDefault对象被释放的时候才保存数据到文件。

这样会有一个问题，就是我的游戏运行时，用户双击Home键，弹出运行的程序，强制停止后，这样会导致玩家的信息丢失，也就是这一把玩的数据没有被写到文件，直接丢失了。同样，游戏crash也会导致数据没有保存。

为什么要改成CCUserDefault对象被释放的时候才保存数据到文件？

我想主要是因为文件IO操作是很浪费资源的，改成这样不断提高了数据读取的效率，而且减少了IO操作。对于游戏是有利的。

由于不能让玩家在强制结束后丢失信息，我又把它改成了setValue的时候保存。

方法：

找到CCUserDefault.cpp下面的

[cpp]static void setValueForKey(const char* pKey, const char* pValue){}  [/cpp]

在该方法最下面加入

[cpp]

if (g_sharedDoc)
{
 xmlSaveFile(CCUserDefault::sharedUserDefault()-&gt;getXMLFilePath().c_str(), g_sharedDoc);
}

[/cpp]