---
title: Unity3D 加载fbx模型，实现简单的方向控制
tags:
  - Unity3D
id: 211
categories:
  - Unity3D
date: 2012-05-10 00:59:43
---

[![](http://www.cocos2dev.com/wp-content/uploads/2012/05/0_1328268839Ey9P.gif.png "0_1328268839Ey9P.gif")](http://www.cocos2dev.com/wp-content/uploads/2012/05/0_1328268839Ey9P.gif.png)

开始学习u3d，记录一些学习的东西，可能很简单。高手勿喷。

在地形中，我放入了一个卡车的模型，通过方向键控制卡车的移动。摄像头跟随卡车的移动。

基本的操作我就不记录了。

1。首先创建地形，添加地形贴图，刷一点草，再种点树。OK。一个简单地形就形成了。

2。卡车是如何控制的？

我们先创建一个cube ，控制一个cube是很简单的，为这个cube添写控制脚本。

&nbsp;

var MoveSpeed=5;

var RotateSpeed=20;

function Update () {

if(Input.GetKey(KeyCode.W)){

this.transform.Translate(Vector3.forward*Time.deltaTime*MoveSpeed);

}else if(Input.GetKey(KeyCode.S)){

this.transform.Translate(Vector3.forward*Time.deltaTime*-MoveSpeed);

}else if(Input.GetKey(KeyCode.A)){

this.transform.Rotate(Vector3.up*Time.deltaTime*-RotateSpeed);

}else if(Input.GetKey(KeyCode.D)){

this.transform.Rotate(Vector3.up*Time.deltaTime*RotateSpeed);

}

}

代码很简单，就是通过键盘对cube进行移动控制。

现在一个cube可以移动了，如何移动卡车呢？

设置cube的position到（0，0，0），设置卡车的position到（0，0，0）。

注意cube和卡车的z轴方向应该一致。缩放cube和卡车一样大，然后将cube的mesh renderer去掉。这样cube外观就不会渲染出来。在层次面板中移动卡车到cube中。

[![](http://www.cocos2dev.com/wp-content/uploads/2012/05/0_1328269661hheY.gif.png "0_1328269661hheY.gif")](http://www.cocos2dev.com/wp-content/uploads/2012/05/0_1328269661hheY.gif.png)

这样移动cube，卡车就被移动了。

最后别忘记了设置camera跟随cube。