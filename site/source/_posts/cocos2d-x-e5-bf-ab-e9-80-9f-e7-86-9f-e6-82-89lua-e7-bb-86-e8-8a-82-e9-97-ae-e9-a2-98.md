---
title: COCOS2D-X 快速熟悉LUA细节问题
tags:
  - cocos2d-x
  - lua
id: 423
categories:
  - Cocos2D-X
date: 2013-04-19 02:31:27
---

这篇博文主要是接着《[COCOS2D-X 快速熟悉LUA基本细节问题](http://www.cocos2dev.com/?p=416)》往下说了下，所以如果是cocos2dx开始学习Lua的话，请先看下《[COCOS2D-X 快速熟悉LUA基本细节问题](http://www.cocos2dev.com/?p=416) 》，主要快速讲了一些Lua的特性，所以快速学习Lua的话，建议先看一遍。

&nbsp;

<span style="color: #ff0000;">一、{}，构造式</span>

{a = 1, b= 2} 等价于 {["a"] = 1,["b"] = 2}

{"x","y","z"} 等价于 {[1] = "x",[2] = "y", [3] = "z"}

&nbsp;

如果真的需要以0为数组的话：

a = {[0] = "x","y","z"}  -- 不推荐这样

&nbsp;

&nbsp;

<span style="color: #ff0000;">二、尽量使用局部变量，便于垃圾收集器释放内存</span>

如果是全局变量，局部使用的时候，有时可以考虑：

local temA = A

&nbsp;

使用局部变量保存全局变量的值，如果后面函数会改变A的值，可以考虑这样，也可以加快当前域的访问速度。

&nbsp;

&nbsp;

<span style="color: #ff0000;">三、控制语句</span>

&nbsp;

1、if then else

&nbsp;

2、while

[cpp]

while true or false do

-- todo

end

[/cpp]

3、repeat-until

[cpp]

repeat

-- todo

until true or false

[/cpp]

4、for

&nbsp;

for 起始，变量， 步长(不写默认1) do

--todo

end

&nbsp;

5、iterator

[cpp]

for k,v in ipairs(a) do

-- k是索引

-- v是对应的value

end

&amp;nbsp;

for k in pairs(a) do

-- k是索引

end

[/cpp]

可以用来逆向table：

[cpp]

revA = {}

for k,v in ipairs(a) do

revA[v] = k

end

[/cpp]

注意：其他的switch、do-while 没有

&nbsp;

&nbsp;

<span style="color: #ff0000;">五、return、break ，只能放在一个块的结束语句（end、else、until）前面</span>

[cpp]

function func()

local x = 0

x = 1

print(x)

&amp;nbsp;

return  -- 想在这里跳出，这样是错误的。

&amp;nbsp;

if(x&gt;1) then

x = 10

end

end

[/cpp]

修改成：

[cpp]

function func()

local x = 0

x = 1

print(x)

&amp;nbsp;

do

return -- 这样才是正确的，可以执行return

end

&amp;nbsp;

if(x&gt;1) then

x = 10

end

end

&amp;nbsp;

[/cpp]

<span style="color: #ff0000;">六、function，函数可以有多个返回值</span>

例如：

[cpp]

function func()

return &quot;a&quot;,&quot;b&quot; --返回2个值

end

[/cpp]

1、如果函数是表达式的最后一个，则函数保留尽可能多的返回值用来赋值变量

[cpp]

x = func() -- x = &quot;a&quot;,返回值&quot;b&quot;不要

x,y = func()  -- x = &quot;a&quot;, y = &quot;b&quot;

x,y,z = 1,func()     -- x = 1, y = &quot;a&quot; , z = &quot;b&quot;  注意func是表达式的最后一个

x,y,z = func() -- x = &quot;a&quot;, y = &quot;b&quot; , z = nil  返回值不够用nil补充

[cpp]

2、如果函数不是表达式的最后一个，则只返回一个值

[cpp]

x,y,z = func(),1 -- x = &quot;a&quot;, y = 1, z = nil

[/cpp]

3、函数在table构造式中，如果函数在构造式的最后一个位置，返回所有值，否则返回一个值

[cpp]

t = {func()} --t = {&quot;a&quot;,&quot;b&quot;}

t = {func(),2} --t = {&quot;a&quot;,2}

[/cpp]

4、函数在return之后，返回所有值

&nbsp;

5、如果函数在()中，则只返回一个值

[cpp]

print(func()) --输出 a b

print((func())) --函数被放在了（）中，输出 a

[/cpp]

6、函数实参可以是变长参数，如果函数实参同时包含了固定参数和变长参数，变长参数必须放在固定参数后面。请注意…的用法

&nbsp;

&nbsp;

<span style="color: #ff0000;">七、如何写带描述的函数实参</span>

OC代码：

[cpp]

- (void) createRect:(float)X y:(float)Y width:(float)Width height:(float)Height

{

//TODO...

}

[/cpp]

有时候函数参数很多，想用oc这种带描述的参数。lua虽然不支持，但是可以用table实现。

[cpp]

function createRect(rectOptions)

if type(rectOptions.title) ~= &quot;string&quot; then

error &quot;need title&quot;

end

&amp;nbsp;

end

&amp;nbsp;

createRect({x = 0,y = 0 , width = 200,height = 300 , title = &quot;hello sunny&quot;})

[/cpp]

提示：函数实参只有一个table时候，可以不要()，所以可以写成：

[cpp]

createRect{x = 0,y = 0 , width = 200,height = 300 , title = &quot;hello sunny&quot;}

[/cpp]