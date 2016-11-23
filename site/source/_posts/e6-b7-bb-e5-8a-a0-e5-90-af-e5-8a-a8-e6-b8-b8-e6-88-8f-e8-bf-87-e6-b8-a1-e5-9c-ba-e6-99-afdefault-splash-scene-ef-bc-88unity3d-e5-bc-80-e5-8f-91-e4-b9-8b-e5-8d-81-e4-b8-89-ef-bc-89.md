---
title: 添加启动游戏过渡场景Default Splash Scene（Unity3D开发之十三）
tags:
  - Splash
  - Unity3D
id: 608
categories:
  - Unity3D
date: 2015-03-07 11:04:51
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！

原文地址: http://blog.csdn.net/cocos2der/article/details/44099095

Unity5个人版会添加Unity Logo作为启动画面，咱们既然没花钱，打个广告也应该。但Unity Logo结束后可以再添加一个自己的启动画面。

下面是添加一个简单的FadeIn->FadeOut过渡场景。

先看下效果图： 
[![20150306140515401](http://www.cocos2dev.com/wp-content/uploads/2015/03/20150306140515401.gif)](http://www.cocos2dev.com/wp-content/uploads/2015/03/20150306140515401.gif)

代码如下 LHSplashScreens.cs：

<pre class="lang:c# decode:true " >using UnityEngine;
using System.Collections;

public enum FadeStatus
{
    FadeIn,
    FadeWaiting,
    FadeOut
}

public class LHSplashScreens : MonoBehaviour 
{
    public string levelToLoad;
    public bool waitForInput;
    public float timeFadingInFinished;
    public Sprite splashSprite;

    private float m_fadeSpeed;
    private float m_waitTime;
    private float m_alpha;
    private FadeStatus m_status;
    private SpriteRenderer m_splashSpriteRenderer;

    public LHSplashScreens()
    {
        levelToLoad = "";
        m_fadeSpeed = 0.3f;
        m_waitTime = 0.5f;
        m_status = FadeStatus.FadeIn;
    }

    void Awake()
    {
        Application.targetFrameRate = 60;
    }

    // Use this for initialization
    void Start () 
    {
        if (Application.levelCount &lt;= 1 || levelToLoad == "")
        {
            Debug.LogWarning("Invalid levelToLoad value.");
        }

        GameObject m_splashSpriteGO = new GameObject("SplashSprite");
        m_splashSpriteGO.AddComponent&lt;SpriteRenderer&gt;();
        m_splashSpriteRenderer = m_splashSpriteGO.GetComponent&lt;SpriteRenderer&gt;();
        m_splashSpriteRenderer.sprite = splashSprite;

        Transform m_splashSpriteTransform = m_splashSpriteGO.gameObject.transform;
        m_splashSpriteTransform.position = new Vector2(0f, 0f);
        m_splashSpriteTransform.parent = this.transform;
    }

    // Update is called once per frame
    void Update () 
    {
        FadeStatus fadeStatus = m_status;
        if (fadeStatus == FadeStatus.FadeIn)
        {
            m_alpha += m_fadeSpeed * Time.deltaTime;
        }
        else if (fadeStatus == FadeStatus.FadeWaiting)
        {
            if ((!waitForInput &amp;&amp; Time.time &gt;= timeFadingInFinished + m_waitTime) || (waitForInput &amp;&amp; Input.anyKey))
            {
                m_status = FadeStatus.FadeOut;
            }
        }
        else if (fadeStatus == FadeStatus.FadeOut)
        {
            m_alpha -= m_fadeSpeed * Time.deltaTime;
        }

        UpdateSplashAlpha();
    }

    private void UpdateSplashAlpha()
    {
        if (m_splashSpriteRenderer != null)
        {
            Color spriteColor = m_splashSpriteRenderer.material.color;
            spriteColor.a = m_alpha;
            m_splashSpriteRenderer.material.color = spriteColor;

            if (m_alpha &gt; 1f)
            {
                m_status = FadeStatus.FadeWaiting;
                timeFadingInFinished = Time.time;
                m_alpha = 1f;
            }

            if (m_alpha &lt; 0)
            {
                if (Application.levelCount &gt;= 1 &amp;&amp; levelToLoad != "")
                {
                    Application.LoadLevel(levelToLoad);
                }
            }
        }
    }
}</pre> 

在你工程中，建立一个新的场景，作为游戏的启动场景。

添加一个Empty GameObject, 添加上面的LHSplashScreens.cs脚本： 
- Level To Load: 完成启动画面后你需要加载的场景 
- Splash Sprite:过渡使用的logo sprite

最后完成后如下：

[![20150306141424055](http://www.cocos2dev.com/wp-content/uploads/2015/03/20150306141424055.png)](http://www.cocos2dev.com/wp-content/uploads/2015/03/20150306141424055.png)
用了markdown之后，停不下来啊。呵呵