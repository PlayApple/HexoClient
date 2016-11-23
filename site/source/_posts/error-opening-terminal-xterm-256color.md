---
title: 'Error opening terminal: xterm-256color'
id: 140
categories:
  - Android
  - Cocos2D-X
date: 2012-04-13 01:55:22
tags:
---

今天./create-android-project.sh时突然出现Error opening terminal: xterm-256color错误。看了下，原来是终端的设置问题。

打开终端的偏好设置，

点击高级，你会看到声明终端选的是xterm-256color。

[![](http://houziadcocos2d.w112.mc-test.com/wp-content/uploads/2012/04/1.png "1")](http://houziadcocos2d.w112.mc-test.com/wp-content/uploads/2012/04/1.png)

&nbsp;

改成xterm-color

重启终端，就好了