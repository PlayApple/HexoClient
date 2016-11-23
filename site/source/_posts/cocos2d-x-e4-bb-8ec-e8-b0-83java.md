---
title: cocos2d-x 从C++调Java
id: 151
categories:
  - Android
  - Cocos2D-X
date: 2012-04-23 12:56:41
tags:
---

最近把IOS的项目跑到Android上，免不了使用JNI，C++掉java还是很简单的。

1、导入头文件

[cpp]

#include &lt;jni.h&gt;

#include &quot;platform/android/jni/JniHelper.h&quot;

[/cpp]

2、实现方法：

[cpp]

JniMethodInfo t;

if (JniHelper::getStaticMethodInfo(t

, &quot;com/cocos2dev/JNIDemo/FMJNIHelp&quot;

, &quot;starPayMoney&quot;

, &quot;(Ljava/lang/String;Ljava/lang/String;)V&quot;))

{

jstring stringArg1 = t.env-&gt;NewStringUTF(&quot;2&quot;);// $

jstring stringArg2 = t.env-&gt;NewStringUTF(&quot;10&quot;);  // count

t.env-&gt;CallStaticVoidMethod(t.classID, t.methodID, stringArg1, stringArg2);

t.env-&gt;DeleteLocalRef(stringArg1);

t.env-&gt;DeleteLocalRef(stringArg2);

t.env-&gt;DeleteLocalRef(t.classID);

}

[/cpp]

注意：

1、com/cocos2dev/JNIDemo/FMJNIHelp指的是java那边需要调用的方法的类名，前面是包名。

2、(Ljava/lang/String;Ljava/lang/String;)V表示传递的参数，两个字符串。