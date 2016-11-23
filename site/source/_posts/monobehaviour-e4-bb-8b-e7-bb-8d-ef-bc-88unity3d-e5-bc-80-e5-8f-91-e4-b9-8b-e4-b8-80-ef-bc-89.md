---
title: MonoBehaviour介绍（Unity3D开发之一）
tags:
  - MonoBehaviour
  - Unity3D
id: 486
categories:
  - Unity3D
date: 2013-12-16 06:02:02
---

猴子自学Unity已经一段时间了，不过还一直没有真正用于商业游戏。这段时间不是很忙，所以想系统的整理下unity技术。都是自己学习中的一些东西，所以深度不是深。

Unity中的脚本都是继承自MonoBehaviour。

# 一、基础函数：

创建脚本就默认的update、start方法：（这些官方的文档都是有的）

Start：Update函数第一次运行前调用，一般用于游戏对象的初始化。比如GetComPonent之类。

Update：每帧调用；一般用于更新场景和状态，物理相关不建议在此处处理。

Awake：脚本实例对象被创建时调用，也可以用于游戏对象的初始化，但是Awake是在所有脚本的Start之前执行。

FixedUpdate：固定间隔执行，一般用于物理状态更新。

LateUpdate：每帧执行，在Update之后。一般和摄像机有关的状态放在这里处理。

&nbsp;

现在我来简单测试下：我在场景中添加了两个Cube，分别添加了Script1，Script2，下面是执行log，可以看到Awake是在所有脚本之前调用的。

[![11](http://www.cocos2dev.com/wp-content/uploads/2013/12/11.jpg)](http://www.cocos2dev.com/wp-content/uploads/2013/12/11.jpg)

&nbsp;

# 二、事件响应函数：

MonoBehaviour中的事件响应函数都是已On开头的，比如：鼠标事件，脚本激活、销毁，碰撞等。

OnApplicationFocus：当玩家获得或失去焦点时发送给所有游戏物体。可以作为协同程序，在函数中使用yield语句即可

OnApplicationPause：当玩家暂停时发送到所有的游戏物体。可以作为协同程序，在函数中使用yield语句即可。

OnApplicationQuit：在应用退出之前发送给所有的游戏物体。当用户停止运行模式时在编辑器中调用。当web被关闭时在网络播放器中被调用。

OnBecameInvisible：当renderer(渲染器)在任何相机上都不可见时调用OnBecameInvisible。

OnBecameVisible：当renderer(渲染器)在任何相机上可见时调用OnBecameVisible。

OnCollisionEnter：当此collider/rigidbody与另一个rigidbody/collider接触时调用。

OnCollisionExit：当此collider/rigidbody停止触发另一个rigidbody/collider时。

OnCollisionStay：当此collider/rigidbody触发另一个rigidbody/collider时，OnCollisionStay将会在每一帧被调用。

OnConnectedToServer：当你成功连接到服务器时，在客户端调用。

OnDisconnectedFromServer：失去连接或从服务器端断开时在客户端调用。

OnFailedToConnectToMasterServer：当连接主服务器出现问题时在客户端或服务器端调用。

OnControllerColliderHit：在移动的时，当controller碰撞到collider时OnControllerColliderHit被调用。

OnDestroy：脚本销毁时调用。

OnDisable：对象禁用或者取消激活时调用。

OnEnable：对象变为可用或激活状态时此函数被调用。

OnDrawGizmosSelected：如果想在物体被选中时绘制gizmos，执行这个函数。

OnGUI：渲染和处理GUI事件时调用。

OnJointBreak：当附在同一对象上的关节被断开时调用。

OnLevelWasLoaded：一个新关卡被载入时此函数被调用。

OnMouseDown、OnMouseDrag、OnMouseEnter、OnMouseExit、OnMouseOver、OnMouseUp：鼠标事件。

OnMouseUpAsButton：只有当鼠标在同一个GUIElement或Collider按下，在释放时调用。

OnTriggerEnter：当Collider(碰撞体)进入trigger(触发器)时调用OnTriggerEnter。

OnTriggerExit：当Collider(碰撞体)停止触发trigger(触发器)时调用OnTriggerExit。

OnTriggerStay：当碰撞体接触触发器时，OnTriggerStay将在每一帧被调用。

# 三、默认组件

组件对应的变量名是组件名的小写。

Transform：对象的位置，角度，缩放。变量名：transform

Rigidbody：刚体属性。

Render：渲染物体模型。

Light：灯光属性。

Camera：相机属性。

Collider：碰撞体属性。

Animation：动画属性。

Audio：声音属性。

# 四、组件获取函数

获取组件的方法由于比较耗时，所以如果你在update修改transform的话，一般可以先在Start中获取这个组件的引用，然后在update中使用引用去修改。

比如获取上面例子cube1的script1脚本：

[c-sharp]Script1 spt1 = GetComponent&lt;Script1&gt;( );[/c-sharp]

下面是获取组件的函数：

GetComponent：获取组件。

GetComponents：获取组件列表。

GetComponentInChildren：返回Type类型组件，在GameObject或它的任何子物体使用深度优先搜索，仅返回激活的组件。

GetComponentsInChildren：在GameObject或任何它的子物体，返回全部Type类型组件。

# 五、获取场景中的游戏对象

同样和获取组件一样，该方法比较费时，一般在Start中获取引用。

[c-sharp]

GameObject cube1 = GameObject.Find(&quot;Cube1&quot;);

GameObject cube1 = GameObject.FindWithTag(&quot;tag_cube1&quot;);

[/c-sharp]

除了上面的方法，也可以使用public对象，直接使用面板拖拽赋值。

&nbsp;