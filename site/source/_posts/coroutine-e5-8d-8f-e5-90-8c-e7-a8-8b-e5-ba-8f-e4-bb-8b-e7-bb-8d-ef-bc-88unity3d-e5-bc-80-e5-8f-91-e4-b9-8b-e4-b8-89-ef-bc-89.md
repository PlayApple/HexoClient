---
title: Coroutine协同程序介绍（Unity3D开发之三）
tags:
  - Coroutine
  - Unity3D
id: 496
categories:
  - Unity3D
date: 2013-12-17 08:51:09
---

Coroutine在Unity3D中叫做协程或协同程序，和多线程类似，也就是说开启协同程序就是开启一个线程。但是在任意指定时刻只有一个协程执行，其他协程挂起。

&nbsp;

Coroutine的相关函数：

StartCoroutine：启动一个协程。

StopCoroutine：终止一个协程。

StopAllCoroutine：终止所有协程。

WaitForSeconds：等待几秒。

WaitForFixedUpdate：等到下一次FixedUpdate调用时执行。

&nbsp;

使用MonoBehaviour.StartCoroutine方法即可开启一个协同程序，也就是说该方法必须在 MonoBehaviour 或继承于MonoBehaviour的类中调用。

<span style="color: #ff6600;">（下面一段是引用与网络上教程，因为讲的很详细了，所以这里直接引用下。为找到原始出处）</span>

注意：在Unity3D中，

StartCoroutine(string methodName)

StartCoroutine(IEnumerator routine)

都可以开启一个线程。

区别在于使用字符串作为参数可以开启线程并在线程结束前终止线程，相反使用IEnumerator 作为参数只能等待线程的结束而不能随时终止(除非使用StopAllCoroutines()方法)；

另外使用字符串作为参数时，开启线程时最多只能传递 一个参数，并且性能消耗会更大一点，而使用IEnumerator 作为参数则没有这个限制。

在Unity3D中，使用StopCoroutine(string methodName)来终止一个协同程序，使用StopAllCoroutines()来终止所有可以终止的协同程序，但这两个方法都只能终止该 MonoBehaviour中的协同程序。

还有一种方法可以终止协同程序，即将协同程序所在gameobject的active属性设置为false，当再次设置active为ture时，协同程序并不会再开启；

如是将协同程序所在脚本的enabled设置为false则不会生效。这是因为协同程序被开启后作为一个线程在运行，而 MonoBehaviour也是一个线程，他们成为互不干扰的模块，除非代码中有调用，他们共同作用于同一个对象，只有当对象不可见才能同时终止这两个线 程。

然而，为了管理我们额外开启的线程，Unity3D将协同程序的调用放在了MonoBehaviour中，这样我们在编程时就可以方便的调用指定脚本 中的协同程序，而不是无法去管理，特别是对于只根据方法名来判断线程的方式在多人开发中很容易出错，这样的设计保证了对象、脚本的条理化管理，并防止了重 名。

<span style="color: #ff0000;">注意：在GameObject.active = false 时他会销毁你的任何一个协同程序过程，并且不会等待过程结束后销毁，GameObject.enable时他并不可以恢复。</span>

协同程序的返回类型为Coroutine类型。在Unity3D中，Coroutine类继承于YieldInstruction，所以，协同程序的返回类型只能为null、等待的帧数(frame)以及等待的时间。

&nbsp;

我写一个例子来看看：

（调试下）TODO

&nbsp;