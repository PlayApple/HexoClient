---
title: 如何修改新建脚本模板-ScriptTemplates（Unity3D开发之十五）
tags:
  - ScriptTemplates
  - Unity3D
id: 615
categories:
  - Unity3D
date: 2015-04-09 03:40:05
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！ 
原文地址: http://blog.csdn.net/cocos2der/article/details/44957631

unity添加新脚本的时候，没有版权文件头信息，主要是没有脚本创建人的姓名，在项目开发中，如果想知道这个脚本是谁写的，呼来唤去搞半天才发现是自己写的！！！

用习惯了xcode，所以准备给unity的新建脚本添加一个信息头内容。

Unity自己就有新建脚本模板文件，但是这个文件里面的预定义key很少，不够咱们使用。所以需要自己添加几个key。然后在新建脚本的时候替换这个几个key为对应的内容。

一、修改模板文件

打开模板文件 Unity.app/Contents/Resources/ScriptTemplates/81-C# Script-NewBehaviourScript.cs.txt 
修改为：

<pre class="lang:default decode:true " >//
//  #SCRIPTNAME##FILEEXTENSION#
//  #PROJECTNAME#
//
//  Created by #SMARTDEVELOPERS# on #CREATIONDATE#.
//
//

using UnityEngine;
using System.Collections;

public class #SCRIPTNAME# : MonoBehaviour {

    // Use this for initialization
    void Start () {

    }

    // Update is called once per frame
    void Update () {

    }

}</pre> 

上面的就是版权头信息，新建的脚本就这下面的效果了：

<pre class="lang:default decode:true " >//
//  TestClass.cs
//  DomoJump
//
//  Created by YanghuiLiu on 04/09/2015.
//
//

using UnityEngine;
using System.Collections;

public class TestClass : MonoBehaviour {

    // Use this for initialization
    void Start () {

    }

    // Update is called once per frame
    void Update () {

    }

}</pre> 

二、添加Editor脚本，用来解析上面我们自己添加的key

<pre class="lang:default decode:true " >//
//  HEScriptKeywordReplace.cs
//  HEUnityExtensionLib
//
//  Created by YanghuiLiu on 04/09/2015.
//
//

using UnityEngine;
using UnityEditor;
using System.Collections;

public class HEScriptKeywordReplace : UnityEditor.AssetModificationProcessor {
    public static void OnWillCreateAsset ( string path ) {
        path = path.Replace(".meta", "");
        int index = path.LastIndexOf(".");
        string file = path.Substring(index);
        if (file != ".cs" &amp;&amp; file != ".js" &amp;&amp; file != ".boo") return;
        string fileExtension = file;

        index = Application.dataPath.LastIndexOf("Assets");
        path = Application.dataPath.Substring(0, index) + path;
        file = System.IO.File.ReadAllText(path);

        file = file.Replace("#CREATIONDATE#", System.DateTime.Now.ToString("d"));
        file = file.Replace("#PROJECTNAME#", PlayerSettings.productName);
        file = file.Replace("#SMARTDEVELOPERS#", PlayerSettings.companyName);
        file = file.Replace("#FILEEXTENSION#", fileExtension);

        System.IO.File.WriteAllText(path, file);
        AssetDatabase.Refresh();
    }
}</pre> 

三、设置PlayerSettings的属性

点击Edit/Project Settings/Player，修改Company Name为所需要的名字。

OK，你也可以自己根据需求自己修改了。