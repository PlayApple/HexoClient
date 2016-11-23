---
title: Quick-Cocos2d-X 捋一捋框架流程
tags:
  - cocos2d-x
  - lua
  - quick-lua
id: 535
categories:
  - Cocos2D-X
date: 2014-03-24 08:10:19
---

一直比较关注Quick Lua，但是项目中一直使用的公司自有的Lua框架，所以一直没机会在实际中使用下Quick Lua。看到群里很多人都在用这个，我在这里梳理下开始使用的流程吧，我主要是说下实际使用上的流程问题。

比如很多学习者甚至不知道enterScene("MainScene") 为什么里面可以是个字符串？当然如果你已经很熟悉框架了，这篇文章就可以跳过了，呵呵。

下面开始吧！

一、前置准备
1、安装下载之类的，官方论坛写的很清楚了，我就不说了。[http://wiki.quick-x.com/doku.php?id=zh_cn:get_started_create_new_project](http://wiki.quick-x.com/doku.php?id=zh_cn:get_started_create_new_project)
2、关于IDE，我使用的IEDA，配置导出的api代码提示，还是挺方便的。[http://wiki.quick-x.com/doku.php?id=zh_cn:get_started_install_intellij_idea](http://wiki.quick-x.com/doku.php?id=zh_cn:get_started_install_intellij_idea)

二、新建一个工程
新建之后，你首先看到的main.lua启动到MyApp.lua。

<pre class="lang:default decode:true " >require("app.MyApp").new():run()</pre> 

看MyApp.lua文件：
1、require("app.MyApp")
这里执行的MyApp.lua的代码是：

<pre class="lang:default decode:true " >local MyApp = class("MyApp", cc.mvc.AppBase)  -- 继承cc.mvc.AppBase
return MyApp</pre> 

这时候，你得到了MyApp这个类（lua关于类的实现网上很多）。

2、require("app.MyApp").new()
MyApp.new()执行后，执行的代码是：

<pre class="lang:default decode:true " >function MyApp:ctor()
    MyApp.super.ctor(self)
end</pre> 

为什么new()了之后会执行MyApp:ctor()？请看function.lua下的function class(classname, super)方法：

<pre class="lang:default decode:true " >function cls.new(...)
            local instance = cls.__create(...)
            -- copy fields from class to native object
            for k,v in pairs(cls) do instance[k] = v end
            instance.class = cls
            instance:ctor(...)
            return instance
end</pre> 

可以看到，在class的实现方法里面，给每个创建的类声明了一个new()方法，方法里面调用了ctor()构造方法（ctor只是个名字，所以不是有些人认为的new了之后，当然会调用构造方法，lua没有类，只是我们模仿了类）

3、require("app.MyApp").new():run()
这时候调用了

<pre class="lang:default decode:true " >function MyApp:run()
    CCFileUtils:sharedFileUtils():addSearchPath("res/")
    self:enterScene("MainScene")
end</pre> 

所以进到了MainScene.lua。

对于MyApp.lua文件，如果我修改成下面的样子，是不是你就理解了上面所做的事情：

<pre class="lang:default decode:true " >-- 声明类
MyApp = class("MyApp", cc.mvc.AppBase) 

--- 类构造方法
--
function MyApp:ctor()
    MyApp.super.ctor(self)
end

--- 对应cpp版的static create()方法
--
function MyApp:create()
    local myApp = MyApp.new()
    myApp:init()
end

--- 你自己的方法
-- @param self 
--
local function launchMainScene(self)
    CCFileUtils:sharedFileUtils():addSearchPath("res/")
    self:enterScene("MainScene")
end

--- init 方法
--
function MyApp:init()
    -- add code here
    launchMainScene(self)
end</pre> 

对应的main.lua将原来的require("app.MyApp").new():run()
修改为：

<pre class="lang:default decode:true " >require("app.MyApp")
MyApp:create()</pre> 

这样你是不是更容易理解了，哈哈。

三、MainScene.lua
enterScene("MainScene") 为什么可以切换场景？
我们看下MyApp的父类AppBase里面：

<pre class="lang:default decode:true " >function AppBase:enterScene(sceneName, args, transitionType, time, more)
    local scenePackageName = self. packageRoot .. ".scenes." .. sceneName
    local sceneClass = require(scenePackageName)
    local scene = sceneClass.new(unpack(totable(args)))
    display.replaceScene(scene, transitionType, time, more)
end</pre> 

这样你能理解了为什么连require文件都没有就能调用MainScene，当然你要留意下，它require时候的文件路径，scene默认写的app/scenes文件夹

好了，其他的应该按照上面的思路基本都能知道为什么了。我就不一一列举了。