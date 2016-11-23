---
title: DontDestroyOnLoad（Unity3D开发之五）
tags:
  - DontDestroyOnLoad
  - Unity3D
id: 563
categories:
  - Unity3D
date: 2014-07-31 09:21:44
---

Unity中我们从A场景切换到B场景的时候，A场景所有对象都会销毁，但有时候我不需要销毁某些东西。

比如一个简单的游戏的背景音乐，我不需要多次重复创建，多个场景播放这一个就行了。这个时候就需要用到DontDestroyOnLoad。

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;

public class MusicManager : MonoBehaviour {
    string volumeSetting;
	void Awake () 
	{
        volumeSetting = PlayerPrefs.GetString("Volume");
        if (volumeSetting == "False")
        {
            AudioListener.volume = 0;
        }
		DontDestroyOnLoad(gameObject);
	}
}</pre> 

我在场景加载的时候初始化场景音乐，之后的进入游戏场景，就不需要重新加载了。

注意：Unity 4.5之前的版本，在来回切换场景的时候会导致多次加载DontDestroyOnLoad的对象，导致出现多个的bug。
如果你是Unity 4.5之前，请使用一个static变量记录你的对象是否已经初始化，防止多次创建。

上面是一个办法，当然你也可以使用单例的方式代替DontDestroyOnLoad。

宣雨松的博客中提到过这种方式，下面一段是宣雨松写的，仅此记录一下。
原文：http://www.xuanyusong.com/archives/2938

首先程序会进入static Global方法中，这个方法永远只会走一遍，所以我在这里创建一个GameObjcet，然后把Global这条脚本绑定上去，我在DontDestroyOnLoad这个对象。

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;

public class Global :MonoBehaviour
{
    public static Global instance;

    static Global()
    {
        GameObject go = new GameObject("Globa");
        DontDestroyOnLoad(go);
        instance = go.AddComponent&lt;Global&gt;();
    }

    public void DoSomeThings()
    {
        Debug.Log("DoSomeThings");
    }

    void Start () 
    {
        Debug.Log("Start");
    }

}</pre> 

这样这条脚本就类似一个静态脚本了，而且这个游戏对象也永远不会因为切换场景而被销毁。而且用起来非常方便。在需要调用它的地方直接调用就行了。

Global.instance.DoSomeThings();