---
title: cocos2d-x 使用JNI从Java调C++
id: 149
categories:
  - Android
  - Cocos2D-X
date: 2012-04-23 12:46:00
tags:
---

最近项目中涉及到Android的移植，要用到java调用c++，方法其实也很简单。

假设java这边我需要调用C++那边的函数void PaySuccess(int type);

1、在java这边申明

[cpp]private static native void nativePaySuccess(int i);[/cpp]

调用时，

[cpp]nativePaySuccess(1);[/cpp]

这样java会调用到C++那边。

2、C++那边实现该方法：

[cpp]

#include &lt;jni.h&gt;

extern &quot;C&quot;

{

void Java_com_cocos2dev_test_JNIDemo_nativePaySuccess(JNIEnv*  env, jobject thiz,jint a)

{

//这里java调用nativePaySuccess(1);，这里就会被调用。jint a = 1

}

}

[/cpp]

<span style="color: #ff0000;">注意：</span>

C++这边请注意一下c++这边方法申明的命名规则，JNIDemo表示java那边方法所在类名，com_cocos2dev_test_该类的包名。nativePaySuccess方法名。

&nbsp;