---
title: cocos2d-x 在CCSequence中使用CCCallFunc时的release BUG
id: 82
categories:
  - Cocos2D-X
date: 2012-04-10 05:12:28
tags:
---

项目中以前一直有个BUG，就是就我使用完CCSequence后，在析构里面release掉的时候总会BUG，之前没时间弄，直接注释了。

今天重新又遇到这个问题，在一哥们的帮助下，找到了原因。但是好的解决方暂时没有。

描述下问题，

[cpp]

mAction= CCSequence::actions(action,
 CCCallFunc::actionWithTarget(this,callfunc_selector(MyClass::callBackAction)),NULL);
mAction-&gt;retain();

[/cpp]

代码某处我创建了一个队列action，当action执行完毕后会回调callBackAction函数。功能很简单。

但是当我在析构里面mAction-&gt;release();时就出错了，纠结半天才知道，当我runAction(mAction)结束后，CCCallFunc会autoRelease掉。那么在析构的时候，我执行mAction-&gt;release();时，当初创建的CCCallFunc会被再次release，但是这个CCCallFunc在前面已经被autoRelease了。这个时候就会报错。

不过呢，CCSequence里面放的都是action时，就不会出这个问题，但action和CCCallFunc都是继承同一个基类，为什么这个会出问题？还不是很清楚。

也许你还会想到析构的时候使用CC_SAFE_RELEASE()，你能想到这很不错，但是也不能解决上面的问题。

先看看它是实现

[cpp]#define CC_SAFE_RELEASE(p)  if(p) { p-&gt;release(); }  [/cpp]

现在知道了吧，他只是判断了传进去的对象是否为空。

OK，就这样吧，记录一笔，午休时间Over，工作中....