---
title: Get and Post（Unity3D开发之六）
tags:
  - Unity3D
  - www
id: 565
categories:
  - Unity3D
date: 2014-08-04 01:57:32
---

unity3d中的www直接提供了web请求服务。使用也非常简单。
<pre class="lang:default decode:true ">using UnityEngine;
using System.Collections.Generic;
using System.Collections;

public class WebManager : MonoBehaviour {

	// Use this for initialization
	void Start () 
	{
		// Request by get
		StartCoroutine(Get("http://www.cocos2dev.com/"));

		// Request by post
		Dictionary&lt;string, string&gt; dic = new Dictionary&lt;string, string&gt; ();
		dic.Add("userId", "6001345679887");
		dic.Add("eventId", "10018");
		StartCoroutine(Post("http://192.168.1.102/api.php", dic));
	}

	// Update is called once per frame
	void Update () 
	{

	}

	// Post
	IEnumerator Post(string url, Dictionary&lt;string, string&gt;postData) 
	{
		WWWForm form = new WWWForm();
		foreach(KeyValuePair&lt;string, string&gt; postArg in postData) 
		{
			form.AddField(postArg.Key, postArg.Value);
		}

		WWW www = new WWW(url, form);
		yield return www;

		if (www.error != null) 
		{
			Debug.Log("error is :"+ www.error);
		} 
		else
		{
			Debug.Log("request result :" + www.text);
		}
	}

	// Get
	IEnumerator Get(string url) 
	{
		WWW www = new WWW (url);
		yield return www;

		if (www.error != null) 
		{
			Debug.Log("error is :"+ www.error);
		} 
		else 
		{
			Debug.Log("request result :" + www.text);
		}
	}
}
</pre>