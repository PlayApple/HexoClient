---
title: cocos2d-x 快速熟悉Lua基本细节问题
tags:
  - cocos2dx
  - lua
id: 416
categories:
  - Cocos2D-X
date: 2013-04-18 06:39:28
---

<span style="color: #ff0000;">一、程序块的写法：</span>

[cpp]

a = 1

b = a*2

a = 1;

b = a*2;

a= 1; b = a*2

a = 1 b = a*2

[/cpp]

这四个程序块都是等价的。

<span style="color: #ff0000;">二、Lua 变量区分大小，~= 是 不等于</span>

<span style="color: #ff0000;">三、注释符号</span>

[cpp]

--[[

这里是多行注释符号

这里是多行注释符号

]]

print(&quot;Sunny&quot;)  -- 两个连接符就是单行注释符号

[/cpp]

注意：多行注释符号一般这样写：

[cpp]

--[[

print(&quot;Sunny&quot;)  --已经被注释了

--]]

[/cpp]

优点：假如你想把注释去掉，直接在开头注释符号前面加一个连接符就可以了。（多行注释符号被改成了单行）

[cpp]

---[[

print(&quot;Sunny&quot;)  --没有注释

--]]

[/cpp]

<span style="color: #ff0000;">四、全局变量不用声明，赋值就可以了，不用的时候赋值nil，未赋值的全局变量为nil</span>

<span style="color: #ff0000;">五、boolean，只有false和nil是假，其他值都是真（包括0和空字符串）</span>

<span style="color: #ff0000;">六、number ，表示双精度浮点数，Lua没有整数类型</span>

<span style="color: #ff0000;">七、string，可以赋值为任意二进制数据</span>

1、注意Lua的字符串是不可变的值，不能像c++那样修改字符串里的字符，需要修改的话请创建一个新的字符创。

[cpp]

a = &quot;hello world&quot; --也可以是单引号，注意统一风格就可以了

b = string.gsub(a,&quot;world&quot;,&quot;sunny&quot;)

print(a) --输出 hello world

print(b) --输出 hello sunny

[/cpp]

2、在字符串上面进行算术操作，lua会将字符串转化成数字

[cpp]

print(&quot;1&quot; + 1) --输出 2

print(&quot;a&quot; + 1) --错误：a string value

[/cpp]

3、tonumber()，字符串转数字

[cpp]

print(tonumber(&quot;123&quot;)) --输出数字 123

print(tonumber(&quot;a123&quot;)) --输出 nil

[/cpp]

3、tostring()，数字转字符串(也可以使用连接符，123 .. "" )

[cpp]

print(type(tostring(123))) --输出 string

print(type(123 .. &quot;&quot;)) --输出 string

[/cpp]

4、#，长度操作符，获取字符串长度

[cpp]

a = &quot;sunny&quot;

print(#a) --输出 5

[/cpp]

<span style="color: #ff0000;">八、table，一种关联数组，也就是一种有索引方式的数组，索引值可以是整数、字符串或其他类型。（nil不可以作为table索引值）</span>

[cpp]

a = {} --创建table,将table的引用存到a

a[&quot;x&quot;] = 1  --增加一个新条目 &quot;x&quot;:1

b = &quot;y&quot;

a[b] = 2    --增加一个新条目 &quot;y&quot;:2

c = a         --c和a引用了同一个table

print(a[&quot;x&quot;]) --输出 1

print(a[&quot;y&quot;]) --输出 2

print(c[&quot;x&quot;]) --输出 1

print(c[&quot;y&quot;]) --输出 2

print(c.y)     --输出 2

[/cpp]

注意：c.y 等价与 c["y"]，但是不同于c[y]

常用技巧：

[cpp]

a = {}

for i = 0,9 do

a[i] = i

end

for i = 1,#a do --#a 返回a的最后索引值

print(a[i]) --利用#输出所有行

end

print(a[#a]) --输出最后一个值

a[#a] = nil --删除最后一个值

a[#a+1] = 10 --添加一个值到最后

[/cpp]

注意：不推荐使用索引0为起始索引，大多数内建函数都假设数组索引开始于1，所以为0的索引数组可能会出现错误。</pre>