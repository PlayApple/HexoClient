---
title: Box2D基本类型说明
tags:
  - Box2D
id: 219
categories:
  - Box2D
date: 2012-05-26 03:47:03
---

<span style="color: #ff0000;">刚体(rigid body)</span>
一块十分坚硬的物质，它上面的任何两点之间的距离都是完全不变的。它们就像钻石那样坚硬。
刚体可以通过b2BodyDef.userData来创建。b2Shape.SetAsBox(width,height)指的是物体的半宽和半高，所以在计算时都要乘以2。由于Box2D本身的限制，运算时要进行长度换算。

<span style="color: #ff0000;">形状(shape)</span>
一块严格依附于物体(body)的 2D 碰撞几何结构(collisiong eometry)。形状具有摩擦(friction)和恢复(restitution)的材料性质。

<span style="color: #ff0000;">约束(constraint)</span>
一个约束(constraint)就是消除物体自由度的物理连接。在 2D中，一个物体有 3 个自由度。如果我 们把一个物体钉在墙上(像摆 那样) ，那我们就把它约束到了墙上。这样，此物体就只能绕着这个钉子旋 转，所以这个约束消除了它 2 个自由度。

<span style="color: #ff0000;">接触约束(contact constraint)</span>
一个防止刚体穿透，以及用于模拟摩擦(friction)和恢复(restitution)的特殊约束。你永远都不必创建 一个接触约束，它们会自动被 Box2D 创建。

<span style="color: #ff0000;">关节(joint)</span>
它是一种用于把两个或多个物体固定到一起的约束。Box2D 支持的关节类型有：旋转，棱柱，距离等 等。关节可以支持限制(limits)和马达(motors)。

<span style="color: #ff0000;">关节限制(joint limit)</span>
一个关节限制(joint limit)限定了一个关节的运动范围。例如人类的胳膊肘只能做某一范围角度的运动。

<span style="color: #ff0000;">关节马达(joint motor)</span>
一个关节马达能依照关节的自由度来驱动所连接的物体。例如，你可以使用一个马达来驱动一个肘的旋转。

<span style="color: #ff0000;">世界(world)</span>
一个物理世界就是物体，形状和约束相互作用的集合。Box2D 支持创建多个世界，但这通常是不必要 的。

<span style="color: #ff0000;">积分器(integrator)</span>
积分器在离散的时间点上模拟物理方程，它将 与游戏动画循环一同运行。通常来说游戏物理引擎需要至少 60Hz 的速度，也就是 1/60 的时间步。

<span style="color: #ff0000;">约束求解器(constraint solver)</span>
约束求解器用于解决模拟中的所有 约束，一次一个。要得到良好的解，需要迭代所有约束多次。建议的 Box2D 迭代次数是 10 次。

<span style="color: #ff0000;">Box2D Body</span>
创建好World后，可以向World内部添加任何球体或者盒子，以及你想到的任何形状的东西，那我就需要定义一个Body。
一个body体大概需要做2-4件事情：
1、定义一个形状
2、一个（x,y）的位置
3、角度
4、一个预制的Sprite对象
其中3、4是可选操作。这里我可能不会讲到Box2D内设的一些画图类库，因为我们在实际的操作中，可能用的都是自定义的。内设的类库一般用来模拟比较好，进行实际开发可能不适合，所以大家有想研究内设画图类库的可能需要自己去研究一下了。

<span style="color: #ff0000;">Box2D b2ShapeDef</span>
如果你想在你的游戏或者其他的什么中具有一些有特色的东西，你可以通过综合形状定义Body来制作一个Sprite。形状的定义，有3种类型的形状定义，他们都是扩展的b2ShapeDef基类。

b2BoxDef类具有4个重要的属性：

1、SetAsBox(设定边框):这是一个向量，本质上说他就是一个形状的中心坐标
2、Density(密度):在碰撞的等式中使用密度*面积=质量，密度如果是0或者null,将会是一个静止的对象。
3、Friction(摩擦力):这用来计算两个对象之间的摩擦，可以在0.0-1.0之间调整它们。
4、Restitution(弹性):这是调整对象弹性程度的属性，可以在0.0-1.0之间调整它们。

b2CircleDef类中有一个不同的属性，代替SetAsBox是他的Radius(半径)。

b2PolyDef类具有一个顶点数组（最大是8）来代替SetAsBox和Radius。这些顶点都是b2Vec2类型的对象。

<span style="color: #ff0000;">Box2D注意事项</span>

全局的对象的构造函数作了三件事情：
1、一个在b2AABB类中的实例构建的坐标系统
2、一个定义重力的向量，这是一个b2Vec2类构建的实例。
3、一个布尔变量来定义对象是否"沉迷"。（如果你设置为true，对象将会沉迷）。
4、执行Step()函数，每一帧都会更新所有的Body在world中的位置。
5、Box2D中的单位为米，30像素==1米。所以在box2D中经常会看到 坐标点乘以30，这样就不奇怪为什么用30而不是15或者20的了。