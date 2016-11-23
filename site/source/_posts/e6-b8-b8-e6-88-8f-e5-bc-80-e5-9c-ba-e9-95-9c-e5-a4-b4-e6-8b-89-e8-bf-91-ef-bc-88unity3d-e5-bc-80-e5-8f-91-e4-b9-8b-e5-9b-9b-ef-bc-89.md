---
title: 游戏开场镜头拉近（Unity3D开发之四）
tags:
  - MoveTowards
  - Unity3D
id: 553
categories:
  - Unity3D
date: 2014-06-01 15:39:52
---

今天看了个Demo，发现它的游戏开场会先有个白色遮罩层的渐变消失，然后镜头拉进到目标场景。镜头拉进主要用Vector3.MoveTowards()

[![11](http://www.cocos2dev.com/wp-content/uploads/2014/06/11.gif)](http://www.cocos2dev.com/wp-content/uploads/2014/06/11.gif)

1、添加一个GUITexture，用作白色遮罩层。设置Active false，事先不可见。不然就挡住你场景了。
2、添加一个EmptyObject，放置在最终希望camera放置的位置。
3、将camera position.z 调整到离目标位置的合适距离。

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;

public class DemoScene : MonoBehaviour {

	public Transform targetPoint; // camera将要移动到的目的地，可以是一个EmptyObject
	public GameObject titleText, whiteMask; //title, 白色的全屏遮罩 （事先在场景中Active为false）

	private int pattern = 0;
	private float alpha = 0.5f;

	// Use this for initialization
	void Start () {
		alpha = 0.5f;
		whiteMask.SetActive(true);
	}

	// Update is called once per frame
	void Update () {

		// 移动camera到指定的地点
		transform.position = Vector3.MoveTowards(transform.position, targetPoint.position, 3 * Time.deltaTime);	

		if(pattern == 0){
			// 慢慢降低白色遮罩层的透明度
			if(alpha &gt; 0){
				alpha -= Time.deltaTime * 0.2f;
    			whiteMask.guiTexture.color = new Color(.5f,.5f,.5f, alpha);
			}

		    // 当接近目标点的时候，显示游戏title
			if(Mathf.Abs(transform.position.z - targetPoint.position.z) &lt;= 6.0f){
				pattern = 1;
				alpha = 0;
				titleText.SetActive(true);
			}
		}

		if(pattern == 1){
			// 慢慢增加透明度，显示title logo
			if(alpha &lt; 0.5f){
				alpha += Time.deltaTime * 0.5f;
    			titleText.guiTexture.color = new Color(.5f,.5f,.5f, alpha);
			}else{
				pattern = 2;
			}
		}

		if(pattern == 2){	
			if(Input.anyKey){
				// 处理后续的事
			}
		}
	}
}
</pre> 