---
title: Unity Singleton 单例类（Unity3D开发之二十）
tags:
  - Singleton
  - Unity3D
id: 638
categories:
  - Unity3D
date: 2015-09-09 03:45:24
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！ 
原文地址: http://blog.csdn.net/cocos2der/article/details/47335197

今天看到群里有朋友问unity单例的最佳实现方式，下面我我经常用的。贴出来供大家参考。

一、添加单例模板类

<pre class="lang:default decode:true " >using UnityEngine;

public class Singleton&lt;T&gt; : MonoBehaviour where T : MonoBehaviour
{
    private static T _instance;

    private static object _lock = new object ();

    public static T Instance 
    {
        get {
            if (applicationIsQuitting) {
                return null;
            }

            lock (_lock) {
                if (_instance == null) {
                    _instance = (T)FindObjectOfType (typeof(T));

                    if (FindObjectsOfType (typeof(T)).Length &gt; 1) {
                        return _instance;
                    }

                    if (_instance == null) {
                        GameObject singleton = new GameObject ();
                        _instance = singleton.AddComponent&lt;T&gt; ();
                        singleton.name = "(singleton) " + typeof(T).ToString ();

                        DontDestroyOnLoad (singleton);
                    }
                }

                return _instance;
            }
        }
    }

    private static bool applicationIsQuitting = false;

    public void OnDestroy ()
    {
        applicationIsQuitting = true;
    }
}
</pre> 
这是一个单例模板类，使用就很简单了。

二、定义自己的单例类
<pre class="lang:default decode:true " >
using UnityEngine;
using System;

public class GameManager : Singleton&lt;GameManager&gt; {
    public float score;
    void Awake () {
        this.Init();
    }

    private void Init() {
        // Init code
    }
}
</pre>
三、调用使用
<pre class="lang:default decode:true " >
GameManager.Instance.score = 99;
</pre>
如果有更好的实现方式，可以推荐下哦。