---
title: cocos2d-x 游戏技能CD实现
tags:
  - 技能CD
id: 64
categories:
  - Cocos2D-X
date: 2012-04-10 04:57:16
---

游戏中技能CD用的最多的也就两种。

一种是类似dota里面技能CD的灰色半透明透明遮罩层，顺时针或者逆时针。

另一种就是类似植物大战僵尸里面的植物建造CD。上下类型的遮罩层。

其实cocos2d-x可以很容易实现这种效果。

dota里面技能CD实现：

[cpp]

CCProgressTimer* pt=CCProgressTimer::progressWithFile(&quot;cd.png&quot;);
mPercentage=100;
pt-&gt;setPercentage(mPercentage);

[/cpp]

cd.png就是半透明的灰色图，这样设置percentage为100，这样的话，cd层就会全部显示出来，覆盖在技能图上。

让cd转起来：

[cpp]pt-&gt;setPercentage((100-mPercentage++)); [/cpp]

OK，就这样就可以实现了，只需要你不停的设置percentage的值就行了。

你应该看到了，我是从100减到0的，因为默认是100的时候Progress填充满。

如果你觉得这样很不爽，要从1到100设置。也可以的，你只需要进入它的源代码处，文件是CCProgressTimer.m，修改一下就行了。

找到这一行代码float alpha = percentage_ /100.f;

在下面加一段代码：

if(alpha !=0.f &amp;&amp; alpha !=1.f)

{

alpha = 1.f-alpha;

}

这样就行了，道理原来的一样。

植物大战僵尸里面技能CD实现：