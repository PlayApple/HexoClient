---
title: PhoneGap 创建XCode工程
tags:
  - PhoneGap
id: 386
categories:
  - IPhone
date: 2013-03-27 08:03:41
---

今天有人问我PhoneGap怎么创建iOS程序，我记得去年写过一个教程，可能创建不成功，现在有空，就写下怎么创建工程吧。

PhoneGap是干什么的？有什么好处，我就不说了，不清楚的自己Google下。

一、下载PhoneGap，

[http://phonegap.com/download/](http://phonegap.com/download/)

下载好压缩包后，解压目录：

[![](http://www.cocos2dev.com/wp-content/uploads/2013/03/phoneGap1.jpg "phoneGap1")](http://www.cocos2dev.com/wp-content/uploads/2013/03/phoneGap1.jpg)
二、创建PhoneGap的XCode工程
打开终端，我用的iTerm。cd到你的PhoneGap目录下的lib/ios/bin目录，运行create命令

<span style="color: #ff0000;">$ ./create 存放目录 com.cocos2dev.app YourAppName</span>

create命令的参数：
第一个是存放你工程的目录，第二个是BundleIdentifier，第三个是你工程的名字

比如我执行
$ ./create MyPro com.cocos2dev.app TestG

执行之后，在你刚才指定的目录会出现一个xcode工程。

[![](http://www.cocos2dev.com/wp-content/uploads/2013/03/phoneGap2.jpg "phoneGap2")](http://www.cocos2dev.com/wp-content/uploads/2013/03/phoneGap2.jpg)
三、运行工程

1、直接用xcode打开创建的工程即可。

2、或者使用PhoneGap提供的命令行编译运行

命令行编译：
进入刚才创建的工程的cordova目录，
<span style="color: #ff0000;">$ ./build</span>

出现 ** BUILD SUCCEEDED ** 表示编译成功。

运行程序：
<span style="color: #ff0000;">$ ./emulate</span>

就可以看到了运行的结果了。

[![](http://www.cocos2dev.com/wp-content/uploads/2013/03/phoneGap3.jpg "phoneGap3")](http://www.cocos2dev.com/wp-content/uploads/2013/03/phoneGap3.jpg)

呵呵，放了个老黄历到www目录