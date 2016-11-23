---
title: Versions比对版本功能 FileMerge 无法找到的解决办法
tags:
  - Versions
id: 287
categories:
  - IPhone
date: 2012-07-26 10:57:12
---

今天用Versions的对比功能查看修改了什么的时候，提示"The FileMerge application could not be located.Please install Xcode Tools from the Apple Developer website."

&nbsp;

我查看Xcode的tool中，可以看到FileMerge，出现这个的原因，一般是直接首次安装xcode4.3.3，系统没有设置xcode路径的原因。从老版本 升级到4.3.3的不会出现该问题。

&nbsp;

解决办法：

&nbsp;

打开终端.输入"sudo xcode-select -switch /Applications/Xcode.app"

我的Xcode是在应用程序中，如果你的不是的话,将/Applications/Xcode.app替换为自己的xcode路径就可以了。

&nbsp;

(xcode直接拖到终端的话，可以自动补全路径的哦)。