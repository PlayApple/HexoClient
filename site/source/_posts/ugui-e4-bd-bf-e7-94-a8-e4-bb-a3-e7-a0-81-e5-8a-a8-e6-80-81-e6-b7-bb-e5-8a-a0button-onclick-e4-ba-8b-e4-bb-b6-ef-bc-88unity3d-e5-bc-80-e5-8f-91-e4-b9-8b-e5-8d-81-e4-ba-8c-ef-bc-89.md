---
title: ' uGUI使用代码动态添加Button.OnClick()事件（Unity3D开发之十二）'
tags:
  - Button
  - uGUI
  - Unity3D
id: 605
categories:
  - Unity3D
date: 2015-03-07 11:01:50
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！

原文地址: http://blog.csdn.net/cocos2der/article/details/42705885

uGUI出来这么久了，也一直没好好用用，主要是公司项目不用U3D。昨晚用了下Button，还是比较爽的。

主要说下用代码添加button.OnClick()事件的方法（使用属性面板添加的方法就不说了）

一、创建2D UI Panel，添加你需要的Button。

我添加了3个Button：BtnStart，BtnShop，BtnLeaderboards。

[![20150114112150640](http://www.cocos2dev.com/wp-content/uploads/2015/03/20150114112150640.png)](http://www.cocos2dev.com/wp-content/uploads/2015/03/20150114112150640.png)
二、添加脚本

<pre class="lang:c# decode:true " >using UnityEngine;  
using System.Collections;  
using System.Collections.Generic;  
using UnityEngine.Events;  
using UnityEngine.UI;  

public class MainMenu : MonoBehaviour {  

    // Use this for initialization  
    void Start () {  
        List&lt;string&gt; btnsName = new List&lt;string&gt;();  
        btnsName.Add("BtnPlay");  
        btnsName.Add("BtnShop");  
        btnsName.Add("BtnLeaderboards");  

        foreach(string btnName in btnsName)  
        {  
            GameObject btnObj = GameObject.Find(btnName);  
            Button btn = btnObj.GetComponent&lt;Button&gt;();  
            btn.onClick.AddListener(delegate() {  
                this.OnClick(btnObj);   
            });  
        }   
    }  

    public void OnClick(GameObject sender)  
    {  
        switch (sender.name)  
        {  
        case "BtnPlay":  
            Debug.Log("BtnPlay");  
            break;  
        case "BtnShop":  
            Debug.Log("BtnShop");  
            break;  
        case "BtnLeaderboards":  
            Debug.Log("BtnLeaderboards");  
            break;  
        default:  
            Debug.Log("none");  
            break;  
        }  
    }  

    // Update is called once per frame  
    void Update () {  

    }  
}  </pre> 

ok，测试一下，可以玩耍了。

================== 2015-03-07 更新 ======================
有人问道如果是Toogle如果添加呢？其实很简单的。

<pre class="lang:c# decode:true " >// Add click listenner for buttons  
        Hashtable btnsName = new Hashtable();  
        btnsName.Add("BtnNewGame", "Button");  
        btnsName.Add("BtnContinue", "Button");  
        btnsName.Add("BtnChallenge", "Button");  
        btnsName.Add("BtnMoreGame", "Button");  
        btnsName.Add("BtnRank", "Button");  
        btnsName.Add("BtnMusic", "Toggle");  
        btnsName.Add("BtnSound", "Toggle");  
        foreach(DictionaryEntry btnInfo in btnsName)   
        {    
            GameObject btnObj = GameObject.Find(btnInfo.Key as string);    
            if (btnInfo.Value == "Button") {  
                Button btn = btnObj.GetComponent&lt;Button&gt;();    
                btn.onClick.AddListener(delegate() {    
                    // this.OnClick(btnObj);     
                });   
            }  
            else if (btnInfo.Value == "Toggle") {  
                Toggle btn = btnObj.GetComponent&lt;Toggle&gt;();    
                btn.onValueChanged.AddListener(delegate(bool isOn) {  
                    // this.OnValueChanged(isOn, btnObj);  
                });  
            }  
        }  </pre> 