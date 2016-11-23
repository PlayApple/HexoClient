---
title: Unity修改Particles Render Material（Unity3D开发之二十三）
tags:
  - Color
  - Particles
  - Unity3D
id: 647
categories:
  - Unity3D
date: 2015-09-11 07:05:34
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！ 
原文地址: http://blog.csdn.net/cocos2der/article/details/48372999

美术在制作粒子特效的时候需要能修改让粒子在某些时刻闪烁，看了下发现没有设置能实现，所以加个脚本给美术使用。

原理就是让美术能控制Render修改Image的Color

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;

public class ParticleBlink : MonoBehaviour {
    public float startBlinkTime = 0.0f;
    public float blinkFrameTime = 0.02f;
    public Color[] blinkColor = new Color[]{
        new Color(255.0f, 255.0f, 255.0f, 0.0f),
        new Color(255.0f, 255.0f, 255.0f, 255.0f)
    };

    private Material m_Material;
    private float m_BlinkLifeTime = 0.0f;
    private bool m_IsStartBlink = false;
    private int m_BlinkColorIndex = 0;

    // Use this for initialization
    void Start () {
        m_Material = GetComponent&lt;Renderer&gt;().material;
        m_BlinkLifeTime = 0;

        if (startBlinkTime &gt; 0) {
            m_IsStartBlink = false;
            Invoke ("StartBlink", startBlinkTime);
        } else {
            m_IsStartBlink = true;
        }
    }

    // Update is called once per frame
    void Update () {
        if (m_IsStartBlink) {
            UpdateColor ();
        }
    }

    void UpdateColor()
    {
        if (blinkColor == null || blinkColor.Length &lt;= 0) {
            return;
        }

        m_BlinkLifeTime += Time.deltaTime;
        if (m_BlinkLifeTime &gt;= blinkFrameTime) {
            m_BlinkLifeTime = 0;

            // Render Material使用不同的Shader，这里的Color变量名需要对应修改
            m_Material.SetColor ("_Color", blinkColor[m_BlinkColorIndex]);
            m_BlinkColorIndex += 1;
            if (m_BlinkColorIndex &gt;= blinkColor.Length) {
                m_BlinkColorIndex = 0;
            }
        }
    }

    void StartBlink()
    {
        m_IsStartBlink = true;
    }
}</pre> 

[![ParticleColor](http://www.cocos2dev.com/wp-content/uploads/2015/09/ParticleColor.png)](http://www.cocos2dev.com/wp-content/uploads/2015/09/ParticleColor.png)
1\. Start Blink Time：开始切换Color的时间
2\. Blink Frame Time：每一个Color保持的时间
3\. Blink Color：待切换的Color数组