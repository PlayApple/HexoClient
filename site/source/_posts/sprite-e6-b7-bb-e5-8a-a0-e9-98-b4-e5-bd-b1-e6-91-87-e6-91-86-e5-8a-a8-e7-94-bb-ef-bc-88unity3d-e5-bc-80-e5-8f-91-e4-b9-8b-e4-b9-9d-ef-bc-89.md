---
title: Sprite添加阴影摇摆动画（Unity3D开发之九）
tags:
  - Unity3D
id: 575
categories:
  - Unity3D
date: 2014-09-09 13:30:23
---

[![Untitled](http://www.cocos2dev.com/wp-content/uploads/2014/09/Untitled.gif)](http://www.cocos2dev.com/wp-content/uploads/2014/09/Untitled.gif)

今天看到一个很简单的摇摆动画，感觉不错的。记录下，以后需要的可以用下，主要是计算position的方式。

DropShadowAnim.cs

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;

public class DropShadowAnim : MonoBehaviour 
{
	public float radius	= 0.15f;		// 阴影距离parent的半径
	public float maxAngle = 45.0f;		// 阴影左右摇摆的最大角度
	public float period = 2.0f;			// 一次摇摆需要多少秒

	private Transform trans;

	void Start()
	{
		trans = this.transform;
	}

	void Update () 
	{
		float phase = Time.realtimeSinceStartup * (2.0f * Mathf.PI) / period;
		float angle = Mathf.Sin(phase) * (Mathf.Deg2Rad * maxAngle);

		float startX = 0.0f;
		float startY = -radius;
		float sinAngle = Mathf.Sin(angle);
		float cosAngle = Mathf.Cos(angle);
		float x = cosAngle * startX - sinAngle * startY;
		float y = sinAngle * startX + cosAngle * startY;

		trans.localPosition = new Vector3(x, y, 0.0f);
	}
}
</pre> 

用起来就非常简单了。

[![0909](http://www.cocos2dev.com/wp-content/uploads/2014/09/0909.png)](http://www.cocos2dev.com/wp-content/uploads/2014/09/0909.png)

1、创建一个Sprite作为parent；
2、复制刚创建的Sprite。拖到1步中，作为其子对象。
3、将2步中的sprite的Sprite Renderer的Color修改为黑色来作为阴影。
4、给2步中的Sprite添加动画脚本。

ok。这样就搞定了。