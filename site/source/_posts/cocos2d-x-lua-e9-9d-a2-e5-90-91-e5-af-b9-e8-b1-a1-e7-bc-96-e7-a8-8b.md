---
title: COCOS2D-X Lua 面向对象编程
tags:
  - cocos2d-x
  - lua
  - Lua面向对象
id: 425
categories:
  - Cocos2D-X
date: 2013-04-22 09:15:44
---

上次有个同学问我，说lua太简单了，没有什么结构，也不好做面向对象编程。其实可以这样说，Lua中的table就是一个对象。

下面我一点一点介绍Lua的面向对象编程。

<span style="color: #ff0000;">一、对象的方法函数：</span>

[cpp]

Hero = {attack = 100}

 function Hero.skill(addAttack)
 Hero.attack = Hero.attack + addAttack
 end

 Hero.skill(20)
 print(Hero.attack) --&gt; 120

[/cpp]

上面的先创建了一个函数，并调用该函数。

仔细的同学可能发现了，调用函数的时候使用了全局Hero。我在上一篇细节介绍中提过，Lua尽量减少全局变量的使用。而且这里也有风险，一不小心修改了Hero，可能Hero就不能正常工作了。

那么联想到上一篇写到的local temA = A，有人可能想到了这样修改上面的函数调用：

[cpp]

oneHero = Hero;
 Hero = nil
 oneHero.skill(30)
 print(oneHero.attack) --&gt; 错误，oneHero为nil

[/cpp]

这样也是错误的。因为Hero已经为nil了。

正确的如何修改呢？我们可以使用this/self 来实现：

[cpp]

Hero = {attack = 100}

 function Hero.skill(self,addAttack)
 self.attack = self.attack + addAttack
 end

 oneHero = Hero;
 Hero = nil
 oneHero.skill(oneHero,30)
 print(oneHero.attack) --&gt; 130

[/cpp]

OK，这下可以了，但是self每次需要自己传，看着就麻烦，其实Lua也可以隐性调用self，我们在修改下：

[cpp]

Hero = {attack = 100}

 function Hero:skill(addAttack)
 self.attack = self.attack + addAttack
 end

 oneHero = Hero;
 Hero = nil
 oneHero:skill(30)
 print(oneHero.attack) --&gt; 130

[/cpp]

请注意上面 : 的使用，冒号可以在方法中添加一个额外的隐藏参数。<span style="color: #ff0000;">上面其实也看到了Hero.skill() 和Hero:skill()的区别。</span>

<span style="color: #ff0000;">二、类，将table作为自己的元表</span>
例如上面的Hero例子，我们修改下:

[cpp]

Hero = {}

 function Hero:new(o)
 o = o or {}
 setmetatable(o,self)
 self.__index = self
 return o
 end

 function Hero:skill(addAttack)
 self.attack = self.attack + addAttack
 end

 oneHero = Hero:new{attack = 90}
 oneHero:skill(10)
 print(oneHero.attack) --&gt;100

[/cpp]

创建一个新英雄的时候，oneHero将Hero设置为自己的元表，当oneHero:skill(10)的时候，在table oneHero中查找skill，没有找到后，会进一步搜索元表的__index。

所以等价于：getmetatable(oneHero).__index.skill(oneHero,10)

而getmetatable(oneHero) 是Hero，Hero.__index还是Hero

所以等价于Hero.skill(oneHero,10)
<span style="color: #ff0000;">三、继承</span>
例如：

[cpp]

Hero = {attack = 0}

 function Hero:new(o)
 o = o or {}
 setmetatable(o,self)
 self.__index = self
 return o
 end

 function Hero:skill(addAttack)
 self.attack = self.attack + addAttack
 end

 function Hero:injured(loseAttack)
 if loseAttack &gt; self.attack then error&quot;not engouth attack&quot; end
 self.attack = self.attack - loseAttack
 end

 HumanHero = Hero:new()

 oneHero = HumanHero:new{attack = 100}
 oneHero:skill(10)

 print(oneHero.attack) --&gt;110

[/cpp]

每个对象没有的方法都会去父类中查找（这里理解为父类），所以如果某个对象需要一些新的属性方法，只需要在该对象中实现就可以了。

&nbsp;

&nbsp;

&nbsp;