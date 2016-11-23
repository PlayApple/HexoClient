---
title: Auto Create Editable Copy Font（Unity3D开发之二十二）
tags:
  - Auto
  - Create Editable Copy
  - Font
  - Unity
id: 645
categories:
  - Unity3D
date: 2015-09-09 09:18:25
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！ 
原文地址: http://blog.csdn.net/cocos2der/article/details/48318879

Unity中咱们经常对字体做Create Editable Copy，虽然已经非常方便了，但是实际使用场景中，有时候会有不方便的地方。

我们经常对同一个字体做不同字体大小的Copy，这个时候，每次都去修改font的属性，非常之蛋疼。
本地化翻译文件导出的文字需要手工加到字体属性中，如果同一个字体还有不同大小，更蛋疼了。
以上你都可以忍了，但是你再次Create Editable Copy之后，你发现场景中已经使用了改字体的Text组件全显示Miss Font
已经不能忍了……

咱们脚本搞定这事，具体实现后效果 
1\. 用配置文件指明需要使用的font name，font size，导出的字符集内容 
2\. 根据配置文件Create Editable Copy，同时保证场景中已存在的组件不丢失字体

一、添加Editor脚本：

<pre class="lang:default decode:true " >using UnityEditor;
using UnityEngine;
using UnityEngine.UI;
using System.Collections.Generic;
using System;
using System.IO;

public  class CreateEditableCopyFont
{

    [MenuItem ("Assets/Create Editable Copy Font")]
    static void CreateFont ()
    {
        TextAsset sourceTextAsset = null;
        try {
            sourceTextAsset = (TextAsset)Selection.activeObject;
        } catch (InvalidCastException e) {
            Debug.Log ("Selected Object is not a txt file: " + Environment.NewLine + e.Message);
        }

        if (sourceTextAsset == null) {
            EditorUtility.DisplayDialog ("No Config selected", "Please select a TxtFile Config...\nSuch as Exsample.txt:\nname=Assets/GameAssets/Fonts/impact.ttf,size=40\n123456789abcdefghijk", "Cancel");
            return;
        }

        int targetFontSize;
        string sourceFontPath, targetFontPath, targetFontCharacters = "";
        try {
            string sourceConfigPath = AssetDatabase.GetAssetPath (Selection.activeObject);
            string[] sourceConfigInfos = sourceTextAsset.text.Split ('\n');
            string headInfo = sourceConfigInfos [0];
            string[] headInfos = headInfo.Split (',');

            sourceFontPath = headInfos [0].Split ('=') [1];
            targetFontPath = sourceConfigPath.Replace (".txt", "_copy");
            targetFontSize = int.Parse (headInfos [1].Split ('=') [1]);
            for (int i = 1; i &lt; sourceConfigInfos.Length; i++) {
                targetFontCharacters += sourceConfigInfos [i];
            }
        } catch (Exception ex) {
            EditorUtility.DisplayDialog ("Config Error", "The config header data error...", "Cancel");
            return;
        }

        // 重新生成字体文件会导致场景中已存在的丢失，
        // 所以需要生成后再次赋值
        string[] targetFontPathInfos = targetFontPath.Split ('/');
        string textCheckName = targetFontPathInfos [targetFontPathInfos.Length - 1];
        List&lt;Text&gt; listTexts = new List&lt;Text&gt; ();
        foreach (Text text in UnityEngine.Object.FindObjectsOfType(typeof(Text))) {
            if (text.font.name == textCheckName) {
                listTexts.Add (text);
            }
        }       

        UnityEngine.Object f = AssetDatabase.LoadMainAssetAtPath (sourceFontPath);
        string path = AssetDatabase.GetAssetPath (f);
        TrueTypeFontImporter fontImporter = AssetImporter.GetAtPath (path) as TrueTypeFontImporter;
        fontImporter.fontTextureCase = FontTextureCase.CustomSet;
        fontImporter.customCharacters = targetFontCharacters;
        fontImporter.fontSize = targetFontSize;
        fontImporter.SaveAndReimport ();
        AssetDatabase.Refresh ();
        Font font = fontImporter.GenerateEditableFont (targetFontPath);

        foreach (Text item in listTexts) {
            item.font = font;
        }

        System.GC.Collect ();
    }
}</pre> 

二、添加配置文件

impact_40.txt

<pre class="lang:default decode:true " >name=Assets/HEGameLib/Examples/Fonts/impact.ttf,size=40
123456789abcdefg,All Characters you want export.</pre> 

注意，配置文件我弄的很简单，第一行是使用的原字体和大小，第二行开始是你的需要导出的字符集。

将该文件放到你需要生成字体的目录，比如：Assets/GameAssets/Fonts/ 下。

三、生成字体
[![CreateEditableCopyFont](http://www.cocos2dev.com/wp-content/uploads/2015/09/CreateEditableCopyFont.png)](http://www.cocos2dev.com/wp-content/uploads/2015/09/CreateEditableCopyFont.png)
右键第二步中放入的配置文件，在弹出菜单中点击 Create Editable Copy Font 
该目录下会生成所需字体。

再次生成该字体，场景中已存在的字体不会丢失。