---
title: Transform介绍（Unity3D开发之二）
tags:
  - Transform
  - Unity3D
id: 491
categories:
  - Unity3D
date: 2013-12-17 07:57:15
---

可能unity中接触较早的就是这个组件了，控制着位置、缩放、方向。

下面简单介绍下：

# 一、成员变量

position：在世界空间坐标transform的位置。

localPosition：相对于父级的变换的位置。如果该变换没有父级，那么等同于Transform.position。

eulerAngles：世界坐标系中的旋转（欧拉角）。

localEulerAngles：相对于父级的变换旋转角度。

right：世界坐标系中的右方向。（世界空间坐标变换的红色轴。也就是x轴。）

up：世界坐标系中的上方向。（在世界空间坐标变换的绿色轴。也就是y轴。）

forward：世界坐标系中的前方向。（在世界空间坐标变换的蓝色轴。也就是z轴。）

rotation：世界坐标系中的旋转（四元数）。

localRotation：相对于父级的变换旋转角度。

localScale：相对于父级的缩放比例。

parent：父对象Transform组件。

worldToLocalMatrix：矩阵变换的点从世界坐标转为自身坐标（只读）。

localToWorldMatrix：矩阵变换的点从自身坐标转为世界坐标（只读）。

root：对象层级关系中的根对象的Transform组件。

childCount：子对象数量。

lossyScale：全局缩放比例（只读）。

# 二、成员函数

Translate：向某方向移动物体多少距离。

Rotate：按照指定欧拉角旋转。

RotateAround：按照多少度在世界坐标的某位置轴旋转物体。

LookAt：旋转物体使z轴指向目标物体。

TransformDirection：将一个方向从局部坐标变换到世界坐标方向。

InverseTransformDirection：将一个方向从世界坐标变换到局部坐标方向。

TransformPoint、InverseTransformPoint。

Description：与所有子物体解除父子关系。

Find：通过名字查找子物体并返回它。如果没有查找到子物体名字，将返回null。如果名字包含“/”字符它将向路径一样穿越层次。
<pre>例如：
[c-sharp]
void Update() {
    // 找到手指并旋转
    aFinger = transform.Find(&quot;Arm/Hand/Finger&quot;);
    aFinger.Rotate(Time.deltaTime*20, 0, 0);

    // 向前移动
    transform.Translate(Vector3.forward * Time.deltaTime * 3)

    // 绕自身坐标Y轴旋转
    transform.Rotate(Vector3.up * Time.deltaTime * 30)

    // 绕世界坐标Y轴旋转
    transform.RotateAround(Vector3.zero, Vector3.up, Time.deltaTime * 30)
 } 
[/c-sharp]