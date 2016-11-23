---
title: 事件/委托机制（event/delegate）（Unity3D开发之十七）
tags:
  - delegate
  - event
  - handle
  - Unity3D
id: 627
categories:
  - Unity3D
date: 2015-06-17 14:53:29
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！ 
原文地址: http://blog.csdn.net/cocos2der/article/details/46539433

Delegate作用我就不多说了，Unity中可以直接使用EventHandler实现事件委托，咱们直接事例吧。

一、场景物体移动结束后事件监听

假如PlayerControl，移动结束后触发MoveComplete事件。

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;
using System;

public class PlayerControl : MonoBehaviour {

    public event EventHandler MoveComplete;

    // Use this for initialization
    void Start () {

    }

    // Update is called once per frame
    void Update () {
        if (Input.GetMouseButtonUp(0)) {
            // Test logic for PlayerMoveComplete
            PlayerMoveComplete();
        }
    }

    void PlayerMoveComplete()
    {
        if (MoveComplete != null) {
            MoveComplete(this, EventArgs.Empty);
        }
    }
}</pre> 
事件接收处理

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;
using System;

public class GameManager : MonoBehaviour {
    public static GameManager Instance;
    public PlayerControl playerControl;

    void Awake () 
    {
        // check there isn't more than one instance of the GameManager in the scene
        if(Instance != null){
            Debug.LogError("More than one GameManager found in the scene");
            return;
        }
        // set the global instance
        Instance = this;
    }

    // Use this for initialization
    void Start () {
        playerControl.MoveComplete += HandleMoveComplete;
    }

    void HandleMoveComplete (object sender, EventArgs e)
    {
        Debug.Log("MoveComplete:");
    }

    // Update is called once per frame
    void Update () {

    }
}</pre> 

这里由于使用的EventHandler实现，所以在事件中无法传递自定义参数。

二、自定义Event，事件中传递自定义参数

1、自定义EventArgs

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;
using System;

public class PlayerMoveEventArgs : EventArgs {

    private string message;

    public PlayerMoveEventArgs(string message)
    {
        this.message = message;
    }

    public string Message
    {
        get{return message;}
    }

}

public delegate void MoveCompleteHandle(object sender, PlayerMoveEventArgs e);</pre> 

那么我们修改下PlayerControl

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;
using System;

public class PlayerControl : MonoBehaviour {

    public event EventHandler MoveComplete;

    public event MoveCompleteHandle CustomMoveComplete;

    // Use this for initialization
    void Start () {

    }

    // Update is called once per frame
    void Update () {
        if (Input.GetMouseButtonUp(0)) {
            // Test logic for PlayerMoveComplete
            PlayerMoveComplete();
        }
    }

    void PlayerMoveComplete()
    {
        if (MoveComplete != null) {
            MoveComplete(this, EventArgs.Empty);
        }

        if (CustomMoveComplete != null) {
            CustomMoveComplete(this, new PlayerMoveEventArgs("Move:" + this.name));
        }
    }
}</pre> 

处理事件的我们也修改下

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;
using System;

public class GameManager : MonoBehaviour {
    public static GameManager Instance;
    public PlayerControl playerControl;

    void Awake () 
    {
        // check there isn't more than one instance of the GameManager in the scene
        if(Instance != null){
            Debug.LogError("More than one GameManager found in the scene");
            return;
        }
        // set the global instance
        Instance = this;
    }

    // Use this for initialization
    void Start () {
        playerControl.MoveComplete += HandleMoveComplete;

        playerControl.CustomMoveComplete += HandleCustomMoveComplete;
    }

    void HandleCustomMoveComplete (object sender, PlayerMoveEventArgs e)
    {
        Debug.Log("HandleCustomMoveComplete:" + e.Message);
    }

    void HandleMoveComplete (object sender, EventArgs e)
    {
        Debug.Log("MoveComplete:");
    }

    // Update is called once per frame
    void Update () {

    }
}</pre> 

ok，现在你可以自由的玩耍了。