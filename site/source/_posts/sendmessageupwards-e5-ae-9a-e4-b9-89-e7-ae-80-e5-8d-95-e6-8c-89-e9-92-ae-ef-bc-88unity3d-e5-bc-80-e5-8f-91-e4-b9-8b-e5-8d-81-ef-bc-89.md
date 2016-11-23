---
title: SendMessageUpwards定义简单按钮（Unity3D开发之十）
tags:
  - SendMessageUpwards
  - Unity3D
id: 582
categories:
  - Unity3D
date: 2014-09-09 15:44:56
---

SendMessageUpwards是朝物体和上级父物体发送信息。也可以用来制作按钮。

下面就是一个简单的GUI交互模式。

一、GUI的接口事件处理类
GUIInterface.cs

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;

public class GUIInterface : MonoBehaviour {

	// Use this for initialization
	void Start () {

	}

	// Update is called once per frame
	void Update () {

	}

	void onTouchStartGame()
	{
		Debug.Log("onTouchStartGame-&gt;");
	}
}
</pre> 

按钮被点击之后调用的handler方法。
MenuItem.cs

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;

// A simple menu item. This simply specifies the handler function to call when the item is clicked/tapped

[RequireComponent(typeof(Collider2D))]
public class MenuItem : MonoBehaviour 
{
	public string handler;		// the name of the handler function to be called when this item is tapped
	public string sfxName;		// the name of a sound clip to play when this item is tapped
}
</pre> 

1、在你的新场景中，创建一个Object对象命名为GUI；
2、创建一个3D Text对象，命名为Start Game；
3、将2步中的Start Game对象拖入1步中的GUI，作为其子对象；
4、将2步中的Start Game添加脚本MenuItem.cs, 添加Box Collider 2D碰撞盒，并勾中Is Trigger。
5、将1步中的GUI对象的Layer修改为GUI

二、输入管理器
InputManager.cs

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;

public class InputManager : MonoBehaviour {
	public LayerMask GUILayerMask;	
	public static InputManager Instance;	

	void Awake () 
	{
		// check there isn't more than one instance of the InputManager in the scene
		if(Instance != null)
		{
			Debug.LogError("More than one InputManager found in the scene");
			return;
		}

		// set the global instance
		Instance = this;
	}

	// Use this for initialization
	void Start () {

	}

	private void UpdateInput()
	{
		bool tapped = false;
		Vector3 tapPos = Vector3.zero;

		// check for mouse input
		if(Input.GetMouseButtonDown(0))
		{
			tapped = true;
			tapPos = Camera.main.ScreenToWorldPoint(Input.mousePosition);
		}

		// check for touch input
		foreach (Touch touch in Input.touches) 
		{
			if (touch.phase == TouchPhase.Began)
			{
				tapped = true;
				tapPos = Camera.main.ScreenToWorldPoint(touch.position);
			}
		}

		// respond to any mouse-down or touch events
		if(tapped)
		{
			// check if there's any gui elements at the tap position
			RaycastHit2D hit = Physics2D.Raycast(tapPos, Vector2.zero, 1.0f, GUILayerMask);
			if (hit.collider != null)
			{
				MenuItem menuItem = hit.collider.gameObject.GetComponent&lt;MenuItem&gt;();
				if(menuItem != null)
				{
                                        if(menuItem.sfxName != "")
					{
                                              // PlaySfx(menuItem.sfxName)
					}
					menuItem.SendMessageUpwards(menuItem.handler);
				}
			}
		}
	}

	void Update () 
	{
		UpdateInput();
	}
}
</pre> 

1、创建Object，命名InputManager，添加脚本InputManager.cs
2、修改属性InputManager GUILayer Mask属性为GUI。

ok。现在完成了。

[![0910](http://www.cocos2dev.com/wp-content/uploads/2014/09/0910.png)](http://www.cocos2dev.com/wp-content/uploads/2014/09/0910.png)

思路：
1、InputManager负责获取手指点击和GUI层对象的碰撞，并检测碰撞体是否含有MenuItem脚本，如果有，则发送MenuItem的handler函数消息。
2、GUIInterface则注册并接受所有GUI层的Item的消息。统一处理交互事件。