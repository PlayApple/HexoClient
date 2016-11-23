---
title: Mac下MonoDevelop中文乱码解决方法
tags:
  - MonoDevelop
id: 312
categories:
  - Unity3D
date: 2012-10-18 12:31:42
---

今天在家里的电脑，MonoDevelop编辑器中文乱码，本想着应该是偏好设置的字体没有设置成中文字体。修改Preferences的font之后，发现还是有乱码问题。

找了下原因，最终解决方法是：

1、在应用程序下找到Unity/MonoDevelop.app，右键显示包内容。找到/Contents/Frameworks/Mono.framework/Versions/2.10.2/etc/gtk-2.0  目录下的gtkrc文件。

用TextWrangler打开(其他文本编辑器也可以)，

找到 font = "Lucida Grande 14"

在下面一行新添加   font_name = "Hei 14" ，保存。

2、启动 MonoDevelop ，打开 Preferences，修改成下面的设置：

[![](http://www.cocos2dev.com/wp-content/uploads/2012/10/1.png "1")](http://www.cocos2dev.com/wp-content/uploads/2012/10/1.png)

&nbsp;

[![](http://www.cocos2dev.com/wp-content/uploads/2012/10/2.png "2")](http://www.cocos2dev.com/wp-content/uploads/2012/10/2.png)

&nbsp;

[![](http://www.cocos2dev.com/wp-content/uploads/2012/10/3.png "3")](http://www.cocos2dev.com/wp-content/uploads/2012/10/3.png)

&nbsp;