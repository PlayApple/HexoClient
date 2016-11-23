---
title: Unity2D Sprite灰白图
tags:
  - gray-shader
  - Unity3D
id: 596
categories:
  - Unity3D
date: 2014-11-10 06:25:44
---

昨晚看到群里问到2DSprite怎么变成灰白图，晚上看了下，修改shader是最方便的。
[![img_graysprite20141110](http://www.cocos2dev.com/wp-content/uploads/2014/11/img_graysprite20141110.png)](http://www.cocos2dev.com/wp-content/uploads/2014/11/img_graysprite20141110.png)
下面是Sprites-Gray.shader的实现，请放入自己工程：

<pre class="lang:default decode:true " >Shader "Sprites/Gray"
{
Properties
{
    [PerRendererData] _MainTex ("Sprite Texture", 2D) = "white" {}
    _Color ("Tint", Color) = (1,1,1,1)
    [MaterialToggle] PixelSnap ("Pixel snap", Float) = 0
}

SubShader
{
    Tags
    { 
        "Queue"="Transparent" 
        "IgnoreProjector"="True" 
        "RenderType"="Transparent" 
        "PreviewType"="Plane"
        "CanUseSpriteAtlas"="True"
    }

    Cull Off
    Lighting Off
    ZWrite Off
    Fog { Mode Off }
    Blend One OneMinusSrcAlpha

    Pass
    {
    CGPROGRAM
        #pragma vertex vert
        #pragma fragment frag
        #pragma multi_compile DUMMY PIXELSNAP_ON
        #include "UnityCG.cginc"

        struct appdata_t
        {
            float4 vertex   : POSITION;
            float4 color    : COLOR;
            float2 texcoord : TEXCOORD0;
        };

        struct v2f
        {
            float4 vertex   : SV_POSITION;
            fixed4 color    : COLOR;
            half2 texcoord  : TEXCOORD0;
        };

        fixed4 _Color;

        v2f vert(appdata_t IN)
        {
            v2f OUT;
            OUT.vertex = mul(UNITY_MATRIX_MVP, IN.vertex);
            OUT.texcoord = IN.texcoord;
            OUT.color = IN.color * _Color;
            #ifdef PIXELSNAP_ON
            OUT.vertex = UnityPixelSnap (OUT.vertex);
            #endif

            return OUT;
        }

        sampler2D _MainTex;

        fixed4 frag(v2f IN) : SV_Target
        {
            fixed4 c = tex2D(_MainTex, IN.texcoord) * IN.color;
            c.rgb *= c.a;
            float gray = dot(c.xyz, float3(0.299, 0.587, 0.114));
            c.xyz = float3(gray, gray, gray);
            return c;
        }
    ENDCG
    }
}
}</pre> 

使用方法：

<pre class="lang:default decode:true " >// Use this for initialization
void Start () {
    SpriteRenderer render = this.GetComponent&lt;SpriteRenderer&gt; ();
    render.material.shader = Shader.Find ("Sprites/Gray");
}</pre> 