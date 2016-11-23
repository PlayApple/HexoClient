---
title: Unity3D Update() 和 FixedUpdate()区别
tags:
  - Unity
id: 307
categories:
  - Unity3D
date: 2012-10-18 05:09:01
---

今天有人问我问什么我在处理物理逻辑的时候把代码放在了FixedUpdate里，而不用Update？

&nbsp;

我说下他们的区别吧，知道了各自的特点，就知道原因了。

&nbsp;

Update() 和 FixedUpdate()在游戏中都会在更新的时候自动循环调用。

&nbsp;

但是<span style="color: #ff0000;">Update是在每次渲染新的一帧的时候才会调用</span>，也就是说，这个函数的更新频率和设备的性能有关以及被渲染的物体（可以认为是三角形的数量）。在性能好的机器上可能fps 30，差的可能小些。这会导致同一个游戏在不同的机器上效果不一致，有的快有的慢。因为Update的执行间隔不一样了。

&nbsp;

而<span style="color: #ff0000;">FixedUpdate，是在固定的时间间隔执行，不受游戏帧率的影响</span>。有点想Tick。所以处理Rigidbody的时候最好用FixedUpdate。

&nbsp;

PS：FixedUpdate的时间间隔可以在项目设置中更改，Edit-&gt;Project Setting-&gt;time  找到Fixed timestep。就可以修改了。