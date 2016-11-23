---
title: 自动生成材质Material（Unity3D开发之十九）
tags:
  - Material
  - texture
  - Unity
id: 635
categories:
  - Unity3D
date: 2015-09-09 03:43:22
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！ 
原文地址: http://blog.csdn.net/cocos2der/article/details/46854411

项目中，有时候导入一些资源时候，需要对应创建材质球，如果每次自己动手创建，还是挺麻烦的，下面是如何导入资源时候自动创建材质球。

<pre class="lang:default decode:true " >using UnityEngine;
using System.Collections;
using System.Collections.Generic;
using UnityEditor;
using System.IO;

public class BuildMaterial : UnityEditor.AssetModificationProcessor
{

    //生成出的Material的路径
    private static string MaterialsPath = "Assets/Resources/Skin/";

    // 创建菜单按钮，手工调用创建材质
    [MenuItem ("HETools/BuildMaterials")]
    static void CreateMateral ()
    {
        Object[] selectObject = Selection.objects;

        List&lt;string&gt; path = new List&lt;string&gt; ();
        foreach (Object obj in selectObject) {
            path.Add (AssetDatabase.GetAssetPath (obj));
        }

        foreach (string p in path) {
            CreateOneMateral (p);
        }
        System.GC.Collect ();
    }

    // 监控assets资源添加，发现指定目录ThemeTile有新增加的texture，就自动生成材质
    public static void OnWillCreateAsset (string path)
    {
        int index = path.LastIndexOf (".");
        string file = path.Substring (index);
        string[] pathArr = path.Split ('/');
        if (pathArr [pathArr.Length - 3] != "ThemeTile")
            return;

        CreateOneMateral (path);
        System.GC.Collect ();
    }

    // 创建材质球
    static void CreateOneMateral (string p)
    {
        p = p.Replace (".meta", "");
        Debug.Log ("CreateOneMateral from path: " + p);

        int pos = p.LastIndexOf ('/');
        if (pos == -1)
            return;

        string[] strArr = p.Split ('/');

        string themeIDStr = strArr [strArr.Length - 2];

        Texture textur = (Texture)AssetDatabase.LoadAssetAtPath (p, typeof(Texture)) as Texture;

        string name = strArr [strArr.Length - 1];

        int y = name.IndexOf ('.');
        name = name.Substring (0, y);
        Material mater = new Material (Shader.Find ("Mobile/VertexLit"));
        mater.mainTexture = textur;

        AssetDatabase.CreateAsset (mater, MaterialsPath + themeIDStr + "/" + name + ".mat");
    }
}</pre> 

[![20150712224503108](http://www.cocos2dev.com/wp-content/uploads/2015/09/20150712224503108.png)](http://www.cocos2dev.com/wp-content/uploads/2015/09/20150712224503108.png)

注意，上面代码中我是规定了只有指定的目录添加texture才会自动生成材质，所以使用时候，请自行修改下。

这里发现了个问题： 
导入贴图时候，自动创建出来的材质球丢失了纹理图，而采用菜单按钮点击创建出来的正常。问题还没有解决，有哪位朋友知道解决办法可以告诉我下。